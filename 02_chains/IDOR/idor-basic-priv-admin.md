# IDORで権限昇格（admin取得）
**Tags:** family:idor / variant:basic / engine:- / pivot:priv / impact:admin  
**Refs:** ASVS（Authorization / Data-specific access）・WSTG（Testing for IDOR）・MITRE ATT&CK（T1190 Exploit Public-Facing Application）・PortSwigger WSA（IDOR）・PayloadsAllTheThings（IDOR）・HackTricks（403/401 Bypasses）  
**Trace:** created: 2025-10-08 / last-review: 2025-10-08 / owner: you

## 概要
数値IDやUUID等の直接参照（Direct Object Reference）をユーザ入力からそのまま用い、**レコード単位の認可（Object-level Authorization）**を行っていない場合、他人のオブジェクトへ**水平移動（horizontal）**が可能になる。さらに、ロール変更や管理APIのメンバー管理のような**機能／データ特有エンドポイント**にも同様の不備があれば、**垂直的な権限昇格（vertical privilege escalation）**に繋がり、一般ユーザが**admin相当の操作**に到達し得る。

## 前提・境界（Non-Destructive）
- 対象はステージング環境を前提。本番は**状態変更リクエスト禁止**（GET/OPTIONSのみ）。  
- テスト用に一般ユーザA/B、管理者Cを用意。A/Bは**同一テナント**に所属。  
- PII/秘匿値はマスク（`****`）。スクリーンショット／HTTPログのみ取得し、**実データ変更は行わない**。  
- 本カードの「書換系」手順は**ステージング限定**とする。

## 入口（Initial Access）
- 代表的機能（context）: **ユーザプロフィール表示／編集API**（例：`/api/v1/users/{userId}`）。  
- 兆候: 連番/推測可能ID、GUIDでも**認可不備**があれば同様に成立。URLパス・クエリ・JSONボディ・ヘッダ中の`userId`や`accountId`。  
- 最小差分のHTTP例（読取のみ）:
```
GET /api/v1/users/1001/profile HTTP/1.1
Cookie: session=****

HTTP/1.1 200
{"id":1001,"email":"****","role":"user"}

GET /api/v1/users/1002/profile HTTP/1.1
Cookie: session=****   # AのCookieのままIDだけ変更

HTTP/1.1 200
{"id":1002,"email":"****","role":"admin"}   # 他人情報取得=IDOR
```

## 横展開（Pivot / Privilege Escalation）
- **データ特有の認可不備**をロール／権限エンドポイントで再現（例：`/api/v1/orgs/{orgId}/members/{userId}/role`）。  
- まず**Allow/権限の露出**を安全に確認：  
```
OPTIONS /api/v1/orgs/200/members/1001/role HTTP/1.1
Cookie: session=****

HTTP/1.1 200
Allow: GET, PATCH   # 書換可能性を示唆（本番はここで止める）
```
- ステージング限定で検証（非破壊配慮のため検証専用テナント／ダミー権限を使用）：
```
PATCH /api/v1/orgs/200/members/1001/role HTTP/1.1
Content-Type: application/json
Cookie: session=****
If-None-Match: "force-safety"   # 失敗する条件付を付与し、実変更を抑止

{"role":"admin"}                 # 実変更は不可。レスポンスの認可判断のみ観察
```
- `403`ではなく**認可通過の痕跡**（`200/202`や意味あるエラーメッセージ、差分レスポンス）が得られれば、**admin相当への昇格可能性**が高い。

## 到達点（Impact）
- 一般ユーザが**管理画面へのアクセス／管理操作**（ユーザ一覧・ロール編集・監査設定・機微データ一括エクスポート等）を実行可能。  
- テナント越境や**他組織管理者の奪取**に発展し、**全ユーザPIIの閲覧／操作、監査回避**等の重大影響。

## 検知・証跡（Detection & Evidence）
- **アプリ監査ログ**：`actorUserId` ≠ `resourceOwnerId` かつ `outcome=Success` の読取/変更イベントを相関。
- **HTTPアクセスログ**：`X-Request-ID`（相関ID）で前後リクエストを連結。JSTで時系列確定。
- 例（アプリ監査ログ・JSONL）:
```
{"ts":"2025-10-08T14:21:03+09:00","reqId":"a1b2","actorUserId":1001,"action":"GET /users/1002/profile","result":"Success"}
{"ts":"2025-10-08T14:22:11+09:00","reqId":"c3d4","actorUserId":1001,"action":"OPTIONS /orgs/200/members/1001/role","result":"Success"}
```
- **検知ルール例**：  
  - 条件: `actorUserId != resourceOwnerId AND action IN {READ, UPDATE} AND result=Success` を**しきい値1回**でアラート（IDORは1回でも重大）。  
  - 追加: `Allow`に`PUT|PATCH|DELETE`を含む機微エンドポイントへ一般ユーザからのアクセスを**高優先度**で検知。

## 是正・防御（Fix / Defense）
- **ASVS 5.0（DoD）に準拠**：  
  - **V8.2.1**: 機能レベルのアクセスを明示的許可ユーザに限定。  
  - **V8.2.2**: **データ特有（record/object-level）アクセス**を**明示的許可**に限定し、**IDOR/BOLAを緩和**。  
  - **V8（監査・ロギング）**: 成功/失敗の認可イベントを記録し相関IDで追跡。  
- 実装要点：  
  - **サーバ側認可**（ABAC/RBAC）で**リソース所有者検証**（例：`SELECT ... WHERE owner_id = :actor_id AND id = :object_id`）。  
  - **ID非公開化**は**補助策**に留め、**必ず認可チェック**を実施（GUID/ハッシュでも必須）。  
  - **多テナント境界**：`tenant_id`の**強制付与**と**包含判定**（`actor.tenant_id == resource.tenant_id`）。  
  - **機能レベル認可**（BFLA対策）：エンドポイント毎に**許可ロール**を定義し、**暗黙許可を禁止**。  
  - **テスト**：A/B/C三者と**水平/垂直**双方のユースケースで単体・統合テストを自動化。

## バリエーション
- **Horizontal**: `/users/{id}`の置換で他人情報を閲覧。  
- **Vertical**: `/members/{userId}/role`などのロール変更APIの認可抜け。  
- **Indirect**: ダウンロードエクスポート（CSV/ZIP）の`jobId`や`fileId`の推測・横取り。  
- **API/Gateway**: GraphQLの`node(id:)`やBFFでの**リゾルバ側認可欠如**。  
- **Hidden fields**: `userId`をフォームに埋め込み、サーバ側で再検証しない。

## 再現手順（Minimal Steps）
1) （Aでログイン）Aの`/api/v1/users/{A}/profile`に**200**でアクセスできることを確認。  
2) 同CookieのままIDだけ`{B}`へ変更→**200**ならIDOR成立（本番はここまで）。  
3) 管理系/ロール系エンドポイント候補を列挙（OpenAPI/JSコード/リンク探索）。  
4) `OPTIONS /orgs/{org}/members/{A}/role`で`Allow`を確認（本番OK）。  
5) （**ステージング限定**）`PATCH`に`If-None-Match`等の安全策を付け応答コードで**認可通過可否**を観測。変更が起きないことを確認し終了。

## 参照
1. OWASP ASVS 5.0（V8.2.x Authorization・Data-specific access）: https://raw.githubusercontent.com/OWASP/ASVS/v5.0.0/5.0/OWASP_Application_Security_Verification_Standard_5.0.0_en.pdf  
   - 参考（ASVS 5.0マッピング・V8.2.2がIDOR/BOLA対策に言及）: https://cornucopia.owasp.org/taxonomy/asvs-5.0/08-authorization/02-general-authorization-design  
2. OWASP WSTG — Testing for Insecure Direct Object References: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References  
3. PortSwigger Web Security Academy — Insecure direct object references (IDOR): https://portswigger.net/web-security/access-control/idor  
   - Burp — Testing for IDORs（手順）: https://portswigger.net/burp/documentation/desktop/testing-workflow/access-controls/testing-for-idors  
4. PayloadsAllTheThings — Insecure Direct Object References: https://swisskyrepo.github.io/PayloadsAllTheThings/Insecure%20Direct%20Object%20References/  
5. HackTricks — 403 & 401 Bypasses（認可回避の周辺手法整理）: https://angelica.gitbook.io/hacktricks/network-services-pentesting/pentesting-web/403-and-401-bypasses  
6. MITRE ATT&CK — T1190 Exploit Public-Facing Application: https://attack.mitre.org/techniques/T1190/
