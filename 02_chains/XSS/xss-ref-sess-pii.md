02_chains/xss-ref-sess-pii.md

# Reflected XSS→Session乗っ取りでPII閲覧
**Tags:** family:xss / variant:ref / engine:- / pivot:sess / impact:pii  
**Refs:** ASVS（V5 Output Encoding, V3 Session）・WSTG（Testing for Reflected XSS）・MITRE ATT&CK（Use Alternate Authentication Material (T1550.004)）・PortSwigger WSA（Reflected XSS）・PayloadsAllTheThings（XSS Injection）・HackTricks（XSS）  
**Trace:** created: 2025-10-07 / last-review: 2025-10-07 / owner: you

## 概要
検索（Search）機能の反射型XSS（Reflected XSS）を起点に、被害者ブラウザで`document.cookie`や`window.name`等へアクセスし、`HttpOnly`不備などの条件下でセッショントークンを奪取。攻撃者は盗んだセッション（Use Alternate Authentication Material）を用いて本人になりすまし、個人情報（PII）閲覧に到達する。

## 前提・境界（Non-Destructive）
- ステージング環境／同意済み範囲のみ。第三者への配信や拡散は禁止。  
- テストユーザのみ使用。実データ・本番アカウント・実トークンは使用しない（値は常に`****`で伏せる）。  
- XSSは**最小限のペイロード**で確認し、持続化や大量リクエストは行わない。  
- セッション再利用は**自己アカウントのクッキー**に限定。PIIはダミーデータで検証。

## 入口（Initial Access）
- 反射点：`/search?q=...`などのパラメータがHTML本文・属性・スクリプト文脈へ無害化（Encoding）なしで反映。  
- 最小PoC（例：Cookie外送想定だが**実送信は行わず**リクエストのみで差分確認）：
```
GET /search?q=%3Cscript%3E/*test*/document.title%3C/script%3E HTTP/1.1
Host: app.example.com
```
- 反射のレスポンス差分（例）：
```
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8

...
<h2>Results for: <script>/*test*/document.title</script></h2>
...
```
- 文脈判定（HTML/Attr/JS/URL）を行い、適切なコンテキストエンコーディング欠落を特定。

## 横展開（Pivot / Privilege Escalation）
- 条件：`HttpOnly`未設定や`document.cookie`等でセッショントークンが取得可能。  
- 盗取したセッションを別ブラウザ／プロファイルで再利用（Use Alternate Authentication Material）。  
- 最小差分例：PII APIに対するCookie再利用
```
GET /me/profile HTTP/1.1
Host: app.example.com
Cookie: session=****; Path=/; Secure
```
- 成功条件：本人UIの再現、`/me/profile`や`/orders`等の認可済みエンドポイントにアクセス可能。

## 到達点（Impact）
- 認可バイパスによるPII閲覧（氏名・メール・住所等）。  
- 二次被害：権限高アカウントのセッション奪取時、より広範なデータアクセスに拡大。  
- 信用失墜・インシデント報告義務・規制罰則（個人情報保護法・GDPR等）リスク。

## 検知・証跡（Detection & Evidence）
- Web/Reverse Proxyアクセスログ：異常なクエリ（`<script>`や`onerror=`等）を検知。  
  - 例：`2025-10-07T13:05:21+09:00 req_id=abc123 client=*** uri="/search?q=%3Cscript%3E..." ua="..."`  
- アプリケーションログ：検索語の入力値・出力テンプレートのレンダリング例外、エラー。  
- CSPレポート（`/csp-report`）：`script-src`違反のPOSTがあれば相関。  
- WAF/シグネチャ：XSSパターン検知イベント（検出時刻JST・相関IDでトレース）。  
- 期待アラート例：  
  - 「Reflected-XSS 疑い：`/search`にスクリプト文脈の入力を検出（req_id=abc123）」  
  - 「Session再利用疑い：短時間に異常な地理IPから同一`session_id`アクセス（sid_hash=****）」

## 是正・防御（Fix / Defense）
- **ASVS準拠のDoD（Definition of Done）**  
  - **V5.3（Output Encoding）**：HTML/Attr/JS/URLの**文脈別**エンコーディングをテンプレート/ビュー層で強制。  
  - **V5.1（Input Validation）**：検索語は文字種・長さの**許容リスト（Allowlist）**で制約。  
  - **V3（Session Management）**：`HttpOnly`/`Secure`/`SameSite=Lax|Strict`付与。トークンはサーバ側で短寿命・ローテーション。  
  - **V14（Configuration / HTTP Headers）**：**CSP**（`default-src 'none'; script-src 'self' 'nonce-<rnd>'; object-src 'none'`等）・`X-Content-Type-Options: nosniff`・`Referrer-Policy`。  
  - **V9（Communication）**：TLS強制、Cookieは`Secure`必須。  
- 追加対策：テンプレートエンジンの自動エスケープ有効化、静的解析/動的スキャンCI、危険API（`innerHTML`）使用禁則。

## バリエーション
- 反射点の違い：HTML本文／属性値／イベントハンドラ／`<script>`直下／URL/JS文字列。  
- 入力媒体：クエリ、POSTフォーム、HTTPヘッダ（Referer/User-Agentの反射）、エラーメッセージ。  
- 迂回：URLエンコード二重化、大小文字混在、テンプレート境界跨ぎ、JSONレスポンス→JS直列化。  
- 防御不備の組合せ：`HttpOnly`欠落＋`SameSite=None`でクロスサイトからの奪取容易化。  

## 再現手順（Minimal Steps）
1) （ステージング）テストユーザでログインし、`/search`に遷移。  
2) `q`にミニマムペイロードを入力して反射点を確認：`<script>/*test*/document.title</script>`（ブロックされる場合は**中止**）。  
3) 条件確認：レスポンス文脈／`Set-Cookie`属性（`HttpOnly`/`SameSite`）を記録。  
4) 自己アカウントのブラウザで**同一ドメイン内**のデベロッパツールを開き、Cookie値（`session=****`）を控える（第三者送信はしない）。  
5) 別プロファイルでCookieを設定し`/me/profile`へアクセス、PIIダミーデータの閲覧可否を確認。証跡（時刻JST・req_id・スクリーンショット）を保存。

## 参照
1. OWASP ASVS v4.0.3 – V5 Input Validation & Encoding / V3 Session Management / V14 Configuration: https://owasp.org/ASVS/  
2. OWASP Web Security Testing Guide – Testing for Reflected Cross-Site Scripting (XSS): https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/01-Testing_for_Reflected_Cross_Site_Scripting  
3. PortSwigger Web Security Academy – Reflected XSS: https://portswigger.net/web-security/cross-site-scripting/reflected  
4. PayloadsAllTheThings – XSS Injection: https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection  
5. HackTricks – XSS (Cross Site Scripting): https://book.hacktricks.xyz/pentesting-web/xss-cross-site-scripting  
6. MITRE ATT&CK – Use Alternate Authentication Material: Web Session Cookie (T1550.004): https://attack.mitre.org/techniques/T1550/004/
