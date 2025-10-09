# ASVS L2（v5.0）× WSTG マッピング（Webペネトレーション実務対応）
**前提**：章タイトル・要件番号は `asvs-l2-web-pentest-guide.md` に準拠。各項目に対応する **WSTG の個別ページ**へ直接リンク（トップページへのリンクは避ける）。

---

## 0. 運用前提（L1と同一）
- 本節は運用ルールの確認であり、WSTGの直接対応はなし（証跡・同意はプロセス管理）。

---

## 1. ブラウザ保護ヘッダ（L2強化）
- **CSP（最低 `object-src 'none'; base-uri 'none'`、nonce/hash 運用）**  
  ASVS：v5.0.0-3.4.3 → WSTG：[Testing Content Security Policy (CSP)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/07-Testing_Content_Security_Policy)
- **`X-Content-Type-Options: nosniff` を全レスポンスに付与**  
  ASVS：v5.0.0-3.4.4 → WSTG：[Testing for Browser Storage and MIME Sniffing (`nosniff`)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/08-Testing_for_Browser_Storage_and_MIME_Sniffing)
- **Referrer-Policy で参照元漏えいを抑止**  
  ASVS：v5.0.0-3.4.5 → WSTG：[Review Webpage Content for Information Leakage（参照元・URL等の露出確認）](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/05-Review_Webpage_Content_for_Information_Leakage)
- **クリックジャッキング対策（`frame-ancestors`）**  
  ASVS：v5.0.0-3.4.6 → WSTG：[Testing for Clickjacking](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/01-Testing_for_Clickjacking)

> 参考（L1継承）：HSTS（3.4.1）/ CORS（3.4.2）/ MIME整合（4.1.1）。

---

## 2. Cookie/セッション（L2の追加観点）
- **Cookie 属性の厳格化（SameSite/HttpOnly、`__Host-`/`__Secure-`）**  
  ASVS：v5.0.0-3.3.2 / 3.3.3 / 3.3.4 → WSTG：[Testing for Cookies Attributes](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes)
- **敏感操作の再認証（メール/電話/MFA/回復情報など）**  
  ASVS：v5.0.0-7.5.1 → WSTG（関連）：[Testing for the Circumvention of Work Flows](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/06-Testing_for_the_Circumvention_of_Work_Flows), [Testing for Bypassing Authorization Schema](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/02-Testing_for_Bypassing_Authorization_Schema)
- **他端末セッションの確認・終了（セッション一覧と個別終了）**  
  ASVS：v5.0.0-7.5.2 → WSTG（関連）：[Testing for Session Timeout](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/04-Testing_for_Session_Timeout), [Testing for Logout Functionality](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/06-Testing_for_Logout_Functionality)
- **認証要素変更後の全セッション終了オプション**  
  ASVS：v5.0.0-7.4.3 → WSTG（関連）：[Testing for Session Management Schema](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/01-Testing_for_Session_Management_Schema), [Testing for Logout Functionality](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/06-Testing_for_Logout_Functionality)
- **ログアウトUIの明確配置（要認証ページから常時到達可）**  
  ASVS：v5.0.0-7.4.4 → WSTG：[Testing for Logout Functionality](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/06-Testing_for_Logout_Functionality)

---

## 3. 認証（Password/MFA/IdP）
### 3.1 Password（UI/仕様の外形）
- **長大パスワード許容（≥64文字）**  
  ASVS：v5.0.0-6.2.9 → WSTG：[Testing for Weak Password Policy](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy)
- **回復フローがMFAをバイパスしない**  
  ASVS：v5.0.0-6.4.3 → WSTG：[Testing for Weak Password Change or Reset Functionalities](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities)

> 下記は **WEBペネ範囲外**（付録Aへ）：6.2.10 / 6.2.11 / 6.2.12

### 3.2 MFA（ワンタイム要素の外形）
- **ワンタイム性（再利用不可）**  
  ASVS：v5.0.0-6.5.1 → WSTG（関連）：[Authentication Testing（総論：2FA/OTPの検査観点）](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/README)

### 3.3 IdP連携（OIDC/SAML）
- **アサーション署名の検証（ID Token/SAML）**  
  ASVS：v5.0.0-6.8.2 → WSTG：[Testing JSON Web Tokens (JWT)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/06-Testing_JSON_Web_Tokens), [Testing for XML Signature Wrapping（SAML関連）](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/20-Testing_for_XML_Signature_Wrapping)
- **SAMLアサーションの一意処理（リプレイ防止）**  
  ASVS：v5.0.0-6.8.3 → WSTG（関連）：[Testing for XML Signature Wrapping](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/20-Testing_for_XML_Signature_Wrapping)

> **acr/amr/auth_time の詳細検証**は設計・運用確認主体のため概要外形のみ（6.8.4 は付録A）。

---

## 4. 入力/サニタイズ・インジェクション（L2追加）
- **LDAP / XPath / LaTeX / 正規表現メタ文字 の注入抑止**  
  ASVS：v5.0.0-1.2.6 / 1.2.7 / 1.2.8 / 1.2.9 → WSTG：  
  [Testing for LDAP Injection](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/16-Testing_for_LDAP_Injection),  
  [Testing for XPath Injection](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/17-Testing_for_XPath_Injection),  
  （LaTeX は対象外に近く、**関連**： [Testing for Server-side Template Injection (SSTI)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/25-Testing_for_Server-side_Template_Injection)）,  
  [Testing for Regular Expression Denial of Service (ReDoS)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/24-Testing_for_ReDoS)
- **テンプレート/Markdown/CSS/XSL/BBCode の無害化**  
  ASVS：v5.0.0-1.3.5 / 1.3.7 → WSTG：  
  [Testing for Server-side Template Injection (SSTI)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/25-Testing_for_Server-side_Template_Injection),  
  [Testing for XSLT Injection](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/22-Testing_for_XSLT_Injection)
- **SVG の危険要素除去（`<script>`/`foreignObject` 等）**  
  ASVS：v5.0.0-1.3.4 → WSTG（関連）：[Testing for DOM-based XSS](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/02-Testing_for_DOM-based_Cross_Site_Scripting), [Testing for Upload of Malicious Files](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-File_Upload_Testing/02-Testing_for_Upload_of_Malicious_Files)
- **SSRF（許可リスト＋危険文字無害化）**  
  ASVS：v5.0.0-1.3.6 → WSTG：[Testing for Server-Side Request Forgery (SSRF)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/21-Testing_for_Server_Side_Request_Forgery)
- **JNDI / フォーマット文字列 / メール注入の無害化（外形で確認）**  
  ASVS：v5.0.0-1.3.8 / 1.3.10 / 1.3.11 → WSTG（関連）：[Testing for Code Injection](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/09-Testing_for_Code_Injection)
- **安全なデシリアライズ（型許可リスト等）**  
  ASVS：v5.0.0-1.5.2 → WSTG：[Testing for Insecure Deserialization](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/19-Testing_for_Insecure_Deserialization)

---

## 5. API/HTTP/GraphQL/WebSocket
- **HTTP→HTTPS 自動リダイレクトはブラウザ向けのみ**  
  ASVS：v5.0.0-4.1.2 → WSTG（関連）：[Testing for HTTP Strict Transport Security (HSTS)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/07-Testing_for_HTTP_Strict_Transport_Security)
- **中継層由来ヘッダの偽装耐性（`X-Forwarded-*` 等）**  
  ASVS：v5.0.0-4.1.3 → WSTG：[Testing for Host Header Injection](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/15-Testing_for_Host_Header_Injection)
- **HTTP Request Smuggling の基本対策**  
  ASVS：v5.0.0-4.2.1 → WSTG：[Testing for HTTP Request Smuggling](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/02-Testing_for_HTTP_Request_Smuggling)
- **GraphQL（イントロスペクション停止／コスト・深さ・件数制限）**  
  ASVS：v5.0.0-4.3.1 / 4.3.2 → WSTG：[GraphQL API Testing](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/05-GraphQL_API_Testing)
- **WebSocket（Origin 検証／専用トークン／初期取得・検証）**  
  ASVS：v5.0.0-4.4.2 / 4.4.3 / 4.4.4 → WSTG：[Testing WebSockets](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/12-Testing_WebSockets)

> 参考（L1継承）：MIME整合（4.1.1）/ WSS使用（4.4.1）

---

## 6. ファイル取扱い（アップロード/ダウンロード）
- **圧縮ファイルの展開前チェック（最大展開サイズ/ファイル数）**  
  ASVS：v5.0.0-5.2.3 → WSTG（関連）：[Testing for Upload of Malicious Files（圧縮・アーカイブの検査観点）](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-File_Upload_Testing/02-Testing_for_Upload_of_Malicious_Files)
- **ダウンロード時のファイル名制御（`Content-Disposition`）**  
  ASVS：v5.0.0-5.4.1 → WSTG（関連）：[Testing for HTTP Response Splitting（ヘッダ注入確認）](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/06-Testing_for_HTTP_Response_Splitting)
- **ファイル名エンコード/サニタイズ（RFC 6266 等）**  
  ASVS：v5.0.0-5.4.2 → WSTG（関連）：[Testing Directory Traversal / File Include（パス正規化・サニタイズ）](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/01-Testing_Directory_Traversal_File_Include)

> 範囲外（文書/運用依存）：許可拡張・最大サイズ（5.1.1）/ AV実施保証（5.4.3）

---

## 7. 認可（Authorization）
- **フィールドレベル認可（BOPLA抑止）**  
  ASVS：v5.0.0-8.2.3 → WSTG：[Testing for Mass Assignment（フィールド権限逸脱の検査）](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_Mass_Assignment)
- **マルチテナント越境の抑止**  
  ASVS：v5.0.0-8.4.1 → WSTG：[Testing for Insecure Direct Object References (IDOR/BOLA)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References)

---

## 8. 自己完結トークン/JWT（受入側の厳格化）
- **トークン種別/目的の誤用防止（ID Token を認可用途に使用しない 等）**  
  ASVS：v5.0.0-9.2.2 → WSTG：[Testing JSON Web Tokens (JWT)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/06-Testing_JSON_Web_Tokens)
- **Audience の検証（`aud` の一致）**  
  ASVS：v5.0.0-9.2.3 → WSTG：[Testing JSON Web Tokens (JWT)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/06-Testing_JSON_Web_Tokens)
- **同一鍵で複数Audience発行時の `aud` 制約**  
  ASVS：v5.0.0-9.2.4 → WSTG：[Testing JSON Web Tokens (JWT)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/06-Testing_JSON_Web_Tokens)

---

## 9. OAuth/OIDC（採用時の外形確認）
### 9.1 クライアント/共通
- **トークンは必要部位のみに送付（BFFではバックエンド限定）**  
  ASVS：v5.0.0-10.1.1 → WSTG：[Testing for OAuth Client Weaknesses](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/04-Testing_for_OAuth_Client)
- **同一UAセッション起点の応答のみ受理（`state`/`nonce`/PKCE 束縛）**  
  ASVS：v5.0.0-10.1.2 → WSTG：[Testing for OAuth Client Weaknesses](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/04-Testing_for_OAuth_Client)
- **コードフローのCSRF対策（PKCE or `state`）**  
  ASVS：v5.0.0-10.2.1 → WSTG：[Testing for OAuth Authorization Server Weaknesses](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/03-Testing_for_OAuth_Authorization_Server)
- **Mix-Up攻撃対策（複数AS連携時）**  
  ASVS：v5.0.0-10.2.2 → WSTG：[Testing for OAuth Client Weaknesses](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/04-Testing_for_OAuth_Client)

### 9.2 リソースサーバ（API）
- **Audience検証／委譲権限に基づく認可／必要時の強度・最近性検証**  
  ASVS：v5.0.0-10.3.1 / 10.3.2 / 10.3.4 → WSTG：[Testing JSON Web Tokens (JWT)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/06-Testing_JSON_Web_Tokens)

### 9.3 認可サーバ（AS）
- **PKCE必須／動的クライアント登録のリスク低減**  
  ASVS：v5.0.0-10.4.6 / 10.4.7 → WSTG：[Testing for OAuth Authorization Server Weaknesses](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/03-Testing_for_OAuth_Authorization_Server)
- **リフレッシュトークン：絶対有効期限／UIからの失効／参照トークン失効**  
  ASVS：v5.0.0-10.4.8 / 10.4.9 → WSTG：[Testing for OAuth Authorization Server Weaknesses](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/03-Testing_for_OAuth_Authorization_Server)
- **バックチャネル要求の機密クライアント認証（PAR/トークン/失効）**  
  ASVS：v5.0.0-10.4.10 → WSTG：[Testing for OAuth Authorization Server Weaknesses](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/03-Testing_for_OAuth_Authorization_Server)
- **必要スコープのみ付与**  
  ASVS：v5.0.0-10.4.11 → WSTG：[Testing for OAuth Authorization Server Weaknesses](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/03-Testing_for_OAuth_Authorization_Server)

### 9.4 OIDCクライアント
- **ID Token再生防止（`nonce`）／ユーザ一意識別（`sub`）／`aud` 検証／悪性ASのなりすまし拒否**  
  ASVS：v5.0.0-10.5.1 / 10.5.2 / 10.5.4 / 10.5.3 → WSTG：[Testing for OpenID Connect](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/07-Testing_for_OpenID_Connect)

### 9.5 OIDCプロバイダ/同意管理（AS側の挙動）
- **許可 `response_mode` の制限（Implicit禁止）／強制ログアウトDoS抑止**  
  ASVS：v5.0.0-10.6.1 / 10.6.2 → WSTG：[Testing for OpenID Connect](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/07-Testing_for_OpenID_Connect)
- **同意の取得・説明・撤回**  
  ASVS：v5.0.0-10.7.1 / 10.7.2 / 10.7.3 → WSTG：[Testing for OpenID Connect](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/07-Testing_for_OpenID_Connect)

> 参考（L1継承）：リダイレクトURI完全一致／コード一回限り・短寿命（10.4.1〜10.4.3）

---

## 10. 構成/公開面・クライアント側データ保護
- **デバッグ無効・ディレクトリリスティング無効・TRACE無効・内部ドキュメント/監視エンドポイント非公開**  
  ASVS：v5.0.0-13.4.2 / 13.4.3 / 13.4.4 / 13.4.5 → WSTG：  
  [Configuration & Deployment Management Testing（総覧）](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/README),  
  [Review HTTP Methods](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/05-Review_HTTP_Methods),  
  [Review Old Backup and Unreferenced Files](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/06-Review_Old_Backups_and_Unreferenced_Files_for_Sensitive_Information)
- **機微データのブラウザキャッシュ抑止（`Cache-Control: no-store` など）**  
  ASVS：v5.0.0-14.3.2 → WSTG：[Testing for Browser Cache Weakness](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/03-Testing_for_Browser_Cache_Weakness)
- **ブラウザストレージに機微データを保存しない（セッショントークン除く）**  
  ASVS：v5.0.0-14.3.3 → WSTG：[Testing for Browser Storage](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/08-Testing_for_Browser_Storage_and_MIME_Sniffing)
- **本番からサンプル/開発用機能を排除**  
  ASVS：v5.0.0-15.2.3 → WSTG：[Review Old Backup and Unreferenced Files](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/06-Review_Old_Backups_and_Unreferenced_Files_for_Sensitive_Information)
- **失敗時の安全動作（フェイルオープン禁止等）**  
  ASVS：v5.0.0-16.5.2 / 16.5.3 → WSTG：[Testing for Error Handling](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/08-Testing_for_Error_Handling)

---

### 備考
- WSTGの章URLは改版で変わる場合があるため、定期的にリンク検証を推奨。  
- L2で「関連」と明記した箇所は、WSTGに専用節がないため最も近い試験観点をマッピング。