# ASVS L1（v5.0）× WSTG マッピング（Webペネトレーション実務対応）
> 本ファイルは **ASVS L1（v5.0）Webペネ実務チェックリスト**の各観点に対し、対応する **WSTG** の具体章をマッピングする。  
> 表記：**ASVS: v5.0.0-〈章.節.要件〉 → WSTG: 〈章タイトル〉（インラインリンク）**

---

## 1. HTTP/TLS/ブラウザ保護（基本の外形)

- **HSTSの有効化**  
  ASVS: v5.0.0-3.4.1 → WSTG: [Testing for HTTP Strict Transport Security (HSTS)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/07-Test_HTTP_Strict_Transport_Security)
- **CORSの許可元固定**  
  ASVS: v5.0.0-3.4.2 → WSTG: [Testing Cross-Origin Resource Sharing (CORS)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/13-Testing_Cross_Origin_Resource_Sharing)
- **コンテンツ種別の整合（`Content-Type` と実体の一致 / `nosniff`）**  
  ASVS: v5.0.0-4.1.1 → WSTG: [Testing for Browser Storage and MIME Sniffing](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/08-Testing_for_Browser_Storage_and_MIME_Sniffing)
- **CSRFの基礎対策（Origin/トークン/メソッド）**  
  ASVS: v5.0.0-3.5.1 / 3.5.2 / 3.5.3 → WSTG: [Testing for Cross-Site Request Forgery (CSRF)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/05-Testing_for_Cross_Site_Request_Forgery)
- **TLSのバージョン/必須化/証明書**  
  ASVS: v5.0.0-12.1.1 / 12.2.1 / 12.2.2 → WSTG: [Testing for Weak Transport Layer Security](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/01-Testing_for_Weak_Transport_Layer_Security)

---

## 2. Cookie/セッションの表層

- **Secure 属性**  
  ASVS: v5.0.0-3.3.1 → WSTG: [Testing for Cookies Attributes (Secure/HttpOnly/SameSite)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes)
- **（参考/L2）HttpOnly・SameSite**  
  ASVS: v5.0.0-3.3.2 / 3.3.4 → WSTG: [Testing for Cookies Attributes](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes)
- **認証直後のセッショントークン再発行（ローテーション）**  
  ASVS: v5.0.0-7.2.4 → WSTG: [Testing for Session Fixation](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/03-Testing_for_Session_Fixation)
- **ログアウト/期限切れ時の無効化**  
  ASVS: v5.0.0-7.4.1 → WSTG: [Testing for Logout Functionality](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/06-Testing_for_Logout_Functionality)

---

## 3. 認証（Authentication）の最低限

- **パスワード最小長 ≥ 8 / よくある弱PWの拒否**  
  ASVS: v5.0.0-6.2.1 / 6.2.4 → WSTG: [Testing for Weak Password Policy](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy)
- **パスワード変更フロー（現行PW要求＋変更可）**  
  ASVS: v5.0.0-6.2.2 / 6.2.3 → WSTG: [Testing for Weak Password Change or Reset Functionalities](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities)
- **複雑性「強制しない」・UI/UX観点（マスク/ペースト/そのまま検証）**  
  ASVS: v5.0.0-6.2.5 / 6.2.6 / 6.2.7 / 6.2.8 → WSTG: [Authentication Testing (Overview)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/README)
- **総当たり抑止（レート制御/ロック）**  
  ASVS: v5.0.0-6.1.1 → WSTG: [Testing for Weak Lock Out Mechanism](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/03-Testing_for_Weak_Lock_Out_Mechanism)

---

## 4. 入力・エンコード/インジェクション（観点の当てどころ）

- **XSS の基礎観察（反射/格納）**  
  ASVS: v5.0.0-1.2.1 / 1.2.3 → WSTG: [Testing for Reflected XSS](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/01-Testing_for_Reflected_Cross_Site_Scripting), [Testing for Stored XSS](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/02-Testing_for_Stored_Cross_Site_Scripting)
- **URL 組立ての安全性（不正リダイレクト等）**  
  ASVS: v5.0.0-1.2.2 → WSTG: [Testing for Client-side URL Redirect](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/09-Testing_for_Client-side_URL_Redirect)
- **SQL インジェクション一次確認**  
  ASVS: v5.0.0-1.2.4 → WSTG: [Testing for SQL Injection](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/05-Testing_for_SQL_Injection)
- **OS コマンドインジェクション一次確認**  
  ASVS: v5.0.0-1.2.5 → WSTG: [Testing for OS Command Injection](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/10-Testing_for_OS_Command_Injection)
- **XXE 対策の外形確認**  
  ASVS: v5.0.0-1.5.1 → WSTG: [Testing for XML External Entity Injection (XXE)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/23-Testing_for_XML_External_Entity_Injection)

---

## 5. 入力検証/ビジネスロジック（最小限）

- **サーバサイド検証の存在**  
  ASVS: v5.0.0-2.2.1 / 2.2.2 → WSTG: [Business Logic Testing (Overview)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/README)
- **手順スキップ耐性（ワークフロー）**  
  ASVS: v5.0.0-2.3.1 → WSTG: [Testing for the Circumvention of Work Flows](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/06-Testing_for_the_Circumvention_of_Work_Flows)

### 認可（Authorization）の最低限

- **機能レベル認可**  
  ASVS: v5.0.0-8.2.1 → WSTG: [Testing for Bypassing Authorization Schema](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/02-Testing_for_Bypassing_Authorization_Schema)
- **データ（オブジェクト）レベル認可**  
  ASVS: v5.0.0-8.2.2 → WSTG: [Testing for Insecure Direct Object References](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References)

---

## 6. ファイル取扱い（アップロード/保存/配信）

- **サイズ上限**  
  ASVS: v5.0.0-5.2.1 → WSTG: [Testing for Upload of Unexpected File Types](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-File_Upload_Testing/01-Testing_for_Upload_of_Unexpected_File_Types)
- **拡張子・内容整合（マジックバイト等）**  
  ASVS: v5.0.0-5.2.2 → WSTG: [Testing for Upload of Malicious Files](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-File_Upload_Testing/02-Testing_for_Upload_of_Malicious_Files)
- **公開ディレクトリ実行抑止**  
  ASVS: v5.0.0-5.3.1 → WSTG: [Testing for File Inclusion / Directory Traversal](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/01-Testing_Directory_Traversal_File_Include)
- **パス操作の無害化（パストラバーサル）**  
  ASVS: v5.0.0-5.3.2 → WSTG: [Testing Directory Traversal / File Include](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/01-Testing_Directory_Traversal_File_Include)

---

## 7. API/WebSocket

- **WebSocket は WSS**  
  ASVS: v5.0.0-4.4.1 → WSTG: [Testing WebSockets](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/12-Testing_WebSockets)

---

## 8. データ保護（外形）

- **URL に機微情報を載せない**  
  ASVS: v5.0.0-14.2.1 → WSTG: [Review Webpage Content for Information Leakage](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/05-Review_Webpage_Content_for_Information_Leakage)
- **ログアウト時のクライアント側クリア**  
  ASVS: v5.0.0-14.3.1 → WSTG: [Testing for Logout Functionality](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/06-Testing_for_Logout_Functionality)
- **データ最小化（不要属性の排除）**  
  ASVS: v5.0.0-15.3.1 → WSTG: [Review Webpage Content for Information Leakage](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/05-Review_Webpage_Content_for_Information_Leakage)

---

## 9. 構成・情報漏えい（軽量外形）

- **`.git` 等のメタデータ非公開**  
  ASVS: v5.0.0-13.4.1 → WSTG: [Review Old Backup and Unreferenced Files for Sensitive Information](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/06-Review_Old_Backups_and_Unreferenced_Files_for_Sensitive_Information)
- **（参考/L2）TRACE 無効・ディレクトリリスティング無効化**  
  ASVS: v5.0.0-13.4.3 / 13.4.4 → WSTG: [Review HTTP Methods](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/05-Review_HTTP_Methods)

---

## 10. OAuth/OIDC（採用している場合のみ）

- **リダイレクトURI許可リスト（完全一致）**  
  ASVS: v5.0.0-10.4.1 → WSTG: [Testing for OAuth Authorization Server Weaknesses](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/03-Testing_for_OAuth_Authorization_Server)
- **認可コードの使い回し不可／短寿命**  
  ASVS: v5.0.0-10.4.2 / 10.4.3 → WSTG: [Testing for OAuth Client Weaknesses](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/04-Testing_for_OAuth_Client)
