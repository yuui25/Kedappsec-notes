<<<BEGIN>>>
# STEP2-2 Session / Cookie ベース認証の構造と観察ポイント

本 STEP では、Web で最も伝統的かつ現在も広く使われている  
**Session / Cookie ベース認証** を体系的に整理し、  
STEP1 の資産情報と STEP2-1 の認証方式分類結果を踏まえて  
「対象サービスが Session 認証を採用しているかを判断できる状態」を作る。

---

## 1. 目的

- Session 認証の仕組み（サーバ側状態管理）の理解  
- Cookie の属性（Secure, HttpOnly, Path, Domain）の意味を整理  
- STEP1 で見つけた `/login` 系 URL に対して、認証方式を正確に分類する基礎を作る  
- 今後の STEP（Session 管理の弱点分析、CSRF・権限分析）につなげる  

---

## 2. 前ステップとの関係

本 STEP は以下の STEP の成果を利用する：

### 使用する STEP1 情報
- **STEP1-3 サブドメイン列挙**  
  → `/login`, `/signin`, `/auth` を持つサブドメインを Session 調査対象にする  
- **STEP1-4 技術スタック推定**  
  → PHP/Laravel、Java(Spring)、Ruby on Rails 等は Session 認証を採用しやすい  
- **STEP1-5 クラウド露出資産**  
  → AWS ALB 配下では Cookie 名に `AWSALB` が含まれることがある  
- **STEP1-6 GitHub/SaaS 情報漏えい**  
  → `.env` に `SESSION_COOKIE=xxx` のような設定値が見つかる場合がある  

### 使用する STEP2-1 情報
- 認証方式の分類結果から「Session である可能性がある URL」を特定  
- Token / OAuth / SAML と区別するための観察項目を補完

---

## 3. 情報源・手法の体系（Session 認証とは何か）

### 3.1 Session 認証の基本構造
- ユーザーが ID/PW を POST  
- サーバ側（アプリ or 認証サーバ）が **Session ID（SID）** を発行  
- クライアントは Cookie で SID を保持  
- 以後のリクエストで SID を送ることで "ログイン済み" を示す

識別要素：  
- `Set-Cookie: sessionid=xxxx`  
- `Set-Cookie: PHPSESSID=...`  
- `JSESSIONID`, `laravel_session`, `sid`, `connect.sid` など技術固有の名称  

---

### 3.2 Cookie 属性による安全性・性質の推定
Cookie の属性からセキュリティレベルと構成を推測できる：

- **Secure:** HTTPS のみ送信  
- **HttpOnly:** JS から読み取れない  
- **SameSite:** CSRF 防御の有無  
- **Domain=:** サブドメイン間共有の範囲  
- **Path=:** アプリケーション内の作用範囲  

これらの設定により、  
後続の PT / TLPT（権限昇格・横展開）の調査で重要な判断材料となる。

---

### 3.3 SPA / API と区別するための特徴
Session 認証は通常：

- Cookie ベース  
- 多くは HTML フォームログイン  
- 302 リダイレクトを多用  

Token ベース認証（SPA）は：

- `Authorization: Bearer`  
- Token を LocalStorage に保存  

OAuth / SAML は：

- 外部 IdP へのリダイレクト  
- `code` / XML などの特有パラメータ  

この違いを観察することで分類が可能。

---

## 4. 実践手順（非侵襲・観察ベース）

### 4.1 STEP1 で収集したログイン URL を対象にアクセスする

~~~~
https://example.com/login
https://auth.example.com/signin
~~~~

観察内容：
- HTML フォームの有無（action パス）  
- method が POST か  
- CSRF トークンフィールドの存在（Session 制御の一部）  

---

### 4.2 開発者ツールで Cookie の変化を確認（最重要）

ログイン前後での変化を比較：

- `Set-Cookie:` ヘッダの増加  
- Cookie 名がフレームワーク特有かどうか  
- Secure / HttpOnly / SameSite 属性  
- Domain 属性で共有範囲が推測できるか  

例：

~~~~
Set-Cookie: laravel_session=abc123; HttpOnly; Secure; SameSite=Lax
~~~~

これは **Laravel + Session 認証** の典型。

---

### 4.3 ログイン後の 302 リダイレクトと Cookie 再設定を観察

Session 認証はログイン後、

- 302 → `/dashboard`  
- Cookie の書き換え  
- CSRF トークンの再生成  

が行われやすい。

SPA/API ではこの挙動は起こらない。

---

### 4.4 HTML / JS からフレームワークを逆算

HTML 内のコメントや JS から推測：

- Laravel Blade のコメント → `/storage/framework/`  
- Rails の meta タグ → `<meta name="csrf-param">`  
- Spring Boot → `JSESSIONID`  
- Express → `connect.sid`  

これにより、Session 認証である可能性が高まる。

---

### 4.5 STEP1-5 クラウド情報との紐付け

AWS ALB 配下なら：

~~~~
Set-Cookie: AWSALB=...
~~~~

この Cookie の存在により  
**ロードバランサ配下 + Session 認証の可能性** が推定できる。

---

## 5. 注意事項

- ログイン試行は行わない  
- Cookie の内容解読や攻撃行為は次ステップで扱うため、本 STEP では禁止  
- 観察だけで認証方式を推定することが目的  
- 本 STEP の成果は後続の「Session 管理の弱点分析」に必ず利用する

---

## 6. 参考文献

- OWASP ASVS V2 Authentication  
- OWASP Session Management Cheat Sheet  
- NIST SP800-63 Digital Identity  
- Framework Docs（Laravel / Spring / Rails / Express）

<<<END>>>