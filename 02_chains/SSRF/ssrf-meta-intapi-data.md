# SSRF→メタデータ→内部API→データ閲覧

**Tags:** family:ssrf / variant:basic / engine:- / pivot:intapi / impact:data  
**Refs:** ASVS（SSRF/URL取込の防御）・WSTG（SSRFのテスト）・MITRE ATT&CK（Exploit Public-Facing Application）・PortSwigger WSA（SSRF）・PayloadsAllTheThings（SSRF）・HackTricks（SSRF）  
**Trace:** created: 2025-10-07 / last-review: 2025-10-07 / owner: you

## 概要
- 対象機能・ユースケース：ユーザー入力URLからサーバ側で取得する機能（画像プロキシ、URLプレビュー、Webhook検証、PDF化、RSS取込 等）。  
- 想定ロール：攻撃者＝一般ユーザー。被害者＝アプリ実行ホスト/メタデータサービス/内部API。  
- 成功条件：サーバ側から外部/内部宛のHTTP(S)アクセスを誘発し、メタデータ or 内部APIの機微情報（トークン/設定/PII）へ到達。

## 前提・境界（Non-Destructive）
- 実施は**ステージング**環境で合意のうえ実施。  
- メタデータサービス(IMDS等)は**ダミー/検証用**を用意。実環境の認証情報取得は**禁止**。  
- 外部到達確認は OAST（コラボレータ等）の**自前検証ドメイン**のみを使用。

## 入口（Initial Access）
- 入口の手掛かり：`url=`, `target=`, `fetch=`, `src=`, `webhookUrl=` などのURLパラメータ、またはJSON/フォーム中のURLフィールド。  
- 最小PoC（外部到達の無害確認）  
  - リクエスト例（GET 例；ヘッダ等は省略）：
    ```
    GET /fetch?url=http://oast.example.test/ping HTTP/1.1
    Host: target.example
    ```
  - 期待：oast 側で HTTP リクエストを受信（User-Agent/日時/接続元IP を記録）。アプリ側レスポンスに取得結果の一部が反映されることもある。  
- 内部到達の確認（ステージング用メタデータ）  
  - リクエスト例：
    ```
    GET /fetch?url=http://169.254.169.254/latest/meta-data/ HTTP/1.1
    Host: target.example
    ```
  - 期待：ステージングのダミーIMDSからメタデータのディレクトリ一覧（例：`iam/`, `hostname` 等）。**本番での実トークン取得は禁止**。

## 横展開（Pivot / Privilege Escalation）
- メタデータ→認証情報の抽出（例：一時クレデンシャル／サービスアカウントトークンの**ダミー**）。  
- 抽出したトークンで**内部API**へアクセス（VPC内ALB/Service Mesh経由や`127.0.0.1:port`等）  
  - 例：`/fetch?url=http://127.0.0.1:8080/admin/api/users`  
- SSRFガジェットの拡張：`file://` や `gopher://` の許可有無、HTTPメソッドの上書き（`X-HTTP-Method-Override`）の可否、ヘッダ注入（Host/SNI）可否の確認。

## 到達点（Impact）
- 技術的到達点：内部APIの未認可呼び出し、機微設定閲覧、サービス間トークンの流出。  
- ビジネス影響：顧客PIIの大量閲覧、内部運用APIの不正操作、アカウント乗っ取り連鎖の足掛かり。  
- 想定深度：標準（外部/内部到達確認）→拡張（内部API列挙）→連鎖完遂（データ取得の再現）。

## 検知・証跡（Detection & Evidence）
- 証跡：  
  - アプリ：対象エンドポイントへのリクエスト・レスポンス原文（マスク済み）、時刻(JST)、相関ID。  
  - OAST：受信ログ（URL/UA/時刻/ソースIP）。  
  - 逆プロキシ/メッシュ：転送ログ、SNI/Host不一致、CONNECT/明示プロトコルの痕跡。  
- ATT&CK Data Source 例：`Application logs`, `Network traffic`, `Web proxy logs`。  
- 期待アラート：外向きHTTPの不審先、IMDSアクセス、ループバック宛アクセス。

## 是正・防御（Fix / Defense）
- 入力制御：**許可リスト(allowlist)** のみ許可（スキーム=`https`、ホストは静的/署名URL限定）。  
- ネットワーク：SSRF対策の**エグレス制御**、IMDSv2強制、ループバック/169.254.169.254/リンクローカル/メタデータホストのブロック。  
- リクエスト実行：サーバ側取得を**無効化/隔離**（専用トランスフォーマ/APIゲートウェイ経由）。  
- レスポンス処理：取得結果の**中継/反映の最小化**、タイムアウト・サイズ制限・リダイレクト制限。  
- 検知：IMDS/内部ホスト宛のアウトバウンドを**継続監視**、逸脱アラート。  
- 回帰防止：URLフェッチ機能の単体/統合テスト（メタデータ保護、ループバック遮断、DNS Rebinding耐性）。

## バリエーション
- **blind-ssrf**（応答非表示）：OASTで外向き到達のみ確認。  
- **dns-rebinding**：ホスト名→内部解決へ切替、キャッシュ・SNI不一致検出。  
- **protocol gadgets**：`gopher://` で任意メソッド/ボディ送出（ステージングのみ）。  
- **ヘッダ注入**：カスタム`Host`/`X-Forwarded-*` が効くか。

## 再現手順（Minimal Steps）
1) `url` 等の入力点を特定。  
2) OAST 宛の最小PoCで**外部到達**を確認（証跡取得）。  
3) ループバック/メタデータ（ステージングIMDS）への最小アクセスで**内部到達**を確認。  
4) 内部APIの**read-only**エンドポイントを最小列挙（差分比較）。  
5) すべてのリクエスト/レスポンス・時刻・相関ID・ログを整理（機微はマスク）して保存。

## 参照
1. OWASP SSRF Prevention Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html  
2. OWASP WSTG（SSRF） — https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/19-Testing_for_Server-Side_Request_Forgery  
3. PortSwigger Web Security Academy（SSRF） — https://portswigger.net/web-security/ssrf  
4. PayloadsAllTheThings（SSRF） — https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery  
5. HackTricks（SSRF） — https://angelica.gitbook.io/hacktricks/pentesting-web/ssrf-server-side-request-forgery  
6. MITRE ATT&CK — Exploit Public-Facing Application (T1190) — https://attack.mitre.org/techniques/T1190/
