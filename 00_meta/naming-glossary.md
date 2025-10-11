00_meta/naming-glossary.md

# 命名規則・用語集

本ドキュメントは `02_chains` のカード名で用いる **語彙の定義と一覧** を提供する。    
各トークンは **小文字・半角英数・ハイフン区切り**、3〜12 文字程度を目安とする（省略は可読性最優先）。

---

## 0. 形式と原則

- **形式**：`<family>-<variant>[-<engine>][-<pivot>][-<impact>].md`（角括弧は任意。3〜5 トークン推奨）
- **意味の優先**：`family`（攻撃クラス） ＞ `variant`（入口/手口の型） ＞ `engine`（スタック差） ＞ `pivot`（横展開の方向） ＞ `impact`（到達点/成果）
- **分割基準**：**1 目的 = 1 カード**。環境差で PoC が大幅に変わる場合のみ `engine` を追加し別カード化。
- **更新運用**：新しい略語を使う前に **本ファイルへ追記**。迷う場合は「候補」に置いてから実運用で検証。

---

## 1. `<family>`（攻撃クラスの大項目）

**定義**：脆弱性クラス／攻撃技法の上位カテゴリ。  
**使い所**：読者が「どの系統の話か」を 1 語で把握できるようにする。

| token | 意味/範囲 | 代表例・備考 |
|---|---|---|
| `xss` | Cross-Site Scripting | `ref`/`stor`/`dom` 等の `variant` と組み合わせ |
| `sqli` | SQL Injection | 型は `variant`（`union`/`bool`/`time`/`error`/`stacked`/`oob`）で表現 |
| `ssrf` | Server-Side Request Forgery | `meta`（IMDS など）/`blind` など |
| `idor` | Insecure Direct Object Reference | 直接参照/リソースID/多テナント境界 |
| `path` | Path Traversal / File Path 操作 | LFI/RFI/ディレクトリトラバーサル等（細分は `pivot`/`variant`） |
| `rce` | Remote Code Execution | 入口は Tpl/Deser/Command など他 family で表現しても可 |
| `cmdi` | OS Command Injection | `blind`/`time` 等の検出型は `variant` で |
| `deser` | Insecure Deserialization | Java/.NET/PHP などは `engine` で差分 |
| `xxe` | XML External Entity | SSRF/ファイル読み取りの連鎖は `pivot` |
| `csrf` | Cross-Site Request Forgery | 同意なし状態変更／SameSite 等対策評価 |
| `open_redirect` | Open Redirect | OAuth 連携の連鎖は `pivot=oauth` など |
| `tpl` | Template Injection（SSTI/CSTI） | サーバ側 SSTI は `tpl-ssti-…`、クライアント側は `tpl-csti-…` |
| `auth` | 認証欠陥/実装不備 | パスワードポリシ/多要素/リカバリ等 |
| `authz` | 認可欠陥/アクセス制御 | 水平/垂直権限・多テナント境界 |
| `proto_pollution` | Prototype Pollution | SSRF/RCE 連鎖は `pivot` で |
| `http_smuggle` | HTTP Request Smuggling | キャッシュ中毒は `pivot=cache` |
| `cache` | Cache Poisoning/Deception |  |
| `cors` | CORS 誤設定 | 認証付き跨り取得の可否など |
| `file_upload` | 不適切なファイルアップロード | 拡張子/Content-Type/置換/画像偽装など |
| `redirect` | リダイレクト系（別名） | `open-redirect` と同義で使用可 |
| `ssti` | サーバ側テンプレート注入 | `tpl-ssti-…` で吸収可能のため要整理 |
| `csti` | クライアント側テンプレート注入 | `tpl-csti-…` で吸収可能 |
| `samesite` | Cookie SameSite 誤設定 | `xss`/`csrf` と交差するため要検討 |
| `header_inject` | ヘッダインジェクション | Host/CRLF などの包括用 |
| `bfl` | Business Logic Flaw | 広すぎるためカード粒度に注意 |
| `subtake` | サブドメインテイクオーバー | DNS/ホスティング連携依存 |

---

## 2. `<variant>`（入口/手口の型）

**定義**：同一 `family` 内での **起点差** を区別する語。検出様式やトリガの違いを表す。

| family（例） | token（variant） | 意味/範囲 |
|---|---|---|
| 共通 | `basic` | 特段の派生がない最小構成 |
| `xss` | `ref` / `stor` / `dom` | 反射 / 格納 / DOM ベース |
| `sqli` | `union` / `bool` / `time` / `error` / `stacked` / `oob` | UNION・真偽・時間・エラー・複数文・帯域外 |
| `ssrf` | `basic` / `meta` / `blind` | 内部到達 / メタデータ到達 / 応答不可（外部観測） |
| `path` | `basic` / `nullbyte` / `filter-bypass` | ../ 系 / ヌルバイト / フィルタ回避 |
| `cmdi` | `basic` / `blind` / `time` | 標準/出力なし/時間差 |
| `deser` | `basic` / `gadget` | 既知 Gadget / 一般論（`engine` 併用推奨） |
| `xxe` | `inband` / `oob` / `blind` | 同帯域 / 帯域外 / 応答なし |
| `auth` | `weak-pass` / `2fa-bypass` / `reset-abuse` |  |
| `authz` | `h-priv` / `v-priv` / `policy-bypass` | 水平 / 垂直 / ポリシ迂回 |
| `tpl` | `ssti` / `csti` | テンプレ注入（サーバ/クライアント） |
| `xss` | `mxss` | Mutation XSS |
| `sqli` | `dns` | DNS exfil ベース |
| `ssrf` | `gopher` / `redirect-chain` | 特殊プロトコル／多段リダイレクト |
| `path` | `symlink` / `zip-slip` | シンボリックリンク/Zip Slip |
| `http_smuggle` | `cl-te` / `te-cl` | モード差 |

---

## 3. `<engine>`（スタック差分：PoC/挙動が変わる環境）

**定義**：**環境差で PoC が大幅に変わる** 場合に限定して付与。DB/テンプレート/クラウド/言語基盤など。

| カテゴリ | token | 例/備考 |
|---|---|---|
| DB | `pg` / `my` / `ms` / `ora` / `sqlite` | PostgreSQL / MySQL / SQL Server / Oracle / SQLite |
| NoSQL | `mongo` / `es` / `redis` | 代表的 NoSQL |
| テンプレ | `freemarker` / `velocity` / `thymeleaf` / `jinja` / `twig` / `mustache` / `hbs` | SSTI で差分大 |
| 言語/ランタイム | `java` / `dotnet` / `php` / `python` / `node` / `ruby` | Deser/Template 差分 |
| クラウド | `aws` / `gcp` / `azure` | SSRF→IMDS/権限連鎖差分 |
| コンテナ/オーケストレータ | `docker` / `k8s` | ソケット到達/K8s API 連鎖 |
| メッセージ基盤 | `kafka` / `rabbitmq` | SSRF/認証迂回での到達差分 |
| 監視/メタデータ | `imds-v1` / `imds-v2` | AWS IMDS バージョン差 |
| SSO/ID基盤 | `keycloak` / `okta` / `auth0` | OAuth/OIDC 実装差分 |

> **注意**：`engine` は乱用しない。**同じ PoC で概ね再現できる**なら省略。

---

## 4. `<pivot>`（横展開・連鎖の方向）

**定義**：入口確立後に **どこへ広げるか** を 1 語で示す。読者が「次の一手」を想像しやすくする。

| token | 意味/範囲 | 代表例 |
|---|---|---|
| `sess` | セッション奪取/固定 | XSS→Cookie/トークン奪取 |
| `priv` | 権限昇格/役割奪取 | IDOR→管理権限 |
| `tenant` | 多テナント越境 | ID 指定/ルーティング破綻 |
| `intapi` | 内部 API 到達 | SSRF→社内 API |
| `lfi` | ローカルファイル閲覧 | Path→`/etc/passwd` 等 |
| `rce` | コード実行到達 | Tpl/Deser/Path→RCE |
| `portscan` | 内部ポート走査 | SSRF でのスキャン |
| `oauth` | OAuth/OIDC 連鎖 | Open Redirect→トークン操作 |
| `cache` | キャッシュ経由の汚染/奪取 | Smuggle/Cache Poisoning |
| `dns` | DNS 経由観測/流出 | XXE/SQLi OOB |
| `s3` / `gcs` | オブジェクトストレージ連鎖 | SSRF→S3/GCS |
| `k8s` | K8s API 連鎖 | SSRF→K8s サービスアカウント |
| `ldap` / `smb` | 目录/SMB 連鎖 | NTLM リレー等 |

---

## 5. `<impact>`（到達点・成果）

**定義**：攻撃連鎖の **実害/ゴール** を 1 語で示す。報告タイトルにも流用しやすい語を採用。

| token | 意味/範囲 | 例 |
|---|---|---|
| `admin` | 管理者権限取得 | 管理画面/ロール奪取 |
| `pii` | 個人情報取得 | 氏名/住所/連絡先 等 |
| `data` | 機密データ取得 | 設計図/設定/秘密情報 |
| `exec` | コード実行 | RCE/任意コマンド |
| `ato` | アカウント乗っ取り | SSO/リカバリ悪用 |
| `dos` | 可用性阻害 | 予約枯渇/高負荷 |
| `integrity` | 改ざん | 支払先/権限変更 |
| `fraud` | 金銭/不正取引 | 支払/ポイント奪取 |
| `ip` | 知財流出 | ソース/モデル |
| `supply` | 供給網汚染 | アップデート改ざん |
| `brand` | 風評/ブランド毀損 | フィッシング拡散 |
| `reg` | 規制違反/罰則 | GDPR 等の逸脱 |

---

## 6. 命名ガイド（実務の流れ）

1. **family を決める**：最も自然な攻撃クラス（例：`xss`）。  
2. **variant を選ぶ**：入口の型（例：`ref`）。  
3. **engine が必要か判定**：PoC が**環境差**で大きく変わるなら付与（例：`-pg`）。  
4. **pivot を付す**：横展開の方向が明確なら 1 語（例：`-sess`）。  
5. **impact で締める**：到達点（例：`-pii`）。

> 例：`xss-ref-sess-pii.md`、`idor-basic-priv-admin.md`、`ssrf-meta-intapi-data.md`、`sqli-union-pg-data.md`

---

## 7. 衝突回避・表記ゆれ

- 同義語は **統一トークン** を採用（例：`open-redirect` に統一、`redirect` は非推奨別名）。  
- 迷ったら「候補」に置いてカード本文中で **補足説明** を入れる。  
- 検索性優先：略語は一般的な**セキュリティ文脈で通じる**形に限定（独自略語は避ける）。

---

## 8. 今後の追加・改訂手順（簡易）

1. 追加したいトークンを **「候補」表** に追記（定義/例/衝突可能性も併記）。  
2. 2〜3 枚の試験運用カードで **可読性/検索性/再現性** を検証。  
3. 問題なければ **「確定」へ昇格**。既存カードを**一括置換**（検索置換指針を `00_meta` に保持）。

---
