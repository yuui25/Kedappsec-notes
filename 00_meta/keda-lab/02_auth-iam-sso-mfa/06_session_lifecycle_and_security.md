<<<BEGIN>>>
# STEP2-6 セッションライフサイクルとログアウト挙動の観察

本 STEP では、これまでに整理した認証方式（Session / OAuth / SAML / MFA）を前提に、  
**「ログイン後のセッションがどのように生まれ・維持され・終了するか」** を整理する。  

セッションライフサイクルを理解することは、  
後の工程（権限分析、攻撃チェーン、TLPT）で  
「どこで・どのように session/information を利用しうるか」を判断する基盤になる。

---

## 1. 目的

- セッションの生成／更新／期限切れ／ログアウトの全体像を理解する  
- Cookie / Token / Assertion がどのタイミングで変化するかを把握する  
- STEP1 で集めた URL 群と、STEP2-1〜5 の認証方式分析結果を用いて  
  対象ごとのセッション挙動を分類できるようにする  
- 後続のステップ（権限・横移動・攻撃チェーン）で使う「セッション地図」を作る準備をする  

---

## 2. 前ステップとの関係（どの情報がここで生きるか）

### 2.1 STEP1 系（外部資産・URL・クラウド）

- **STEP1-3 サブドメイン列挙**  
  - `/login`, `/logout`, `/signout`, `/session`, `/account` の発見  
- **STEP1-4 技術スタック推定**  
  - Laravel / Rails / Spring / Express など、Session 管理の傾向を意識する  
- **STEP1-5 クラウド露出資産**  
  - ALB / API Gateway / CloudFront 配下のサービスで Cookie や Token の扱いが変わる  
- **STEP1-6 GitHub / CI/CD / SaaS**  
  - `.env` の `SESSION_LIFETIME`, `JWT_EXP`, `OIDC_SESSION` などの設定値  

### 2.2 STEP2 系（認証方式）

- **STEP2-2 Session/Cookie 認証**  
  - セッション ID が Cookie としてどのように発行されるかを把握済み  
- **STEP2-3 OAuth/OIDC**  
  - Access Token / Refresh Token の存在と有効期限  
- **STEP2-4 SAML/SSO**  
  - Assertion を受け取った後、アプリ側でどのように Session を持つか  
- **STEP2-5 MFA**  
  - MFA 前後でセッションや Token がどう変化するかを観察対象にする  

これらの情報を前提として、  
**「セッションがどこで・どう維持・終了しているか」を観察する** のが本 STEP の焦点となる。

---

## 3. 情報源・手法の体系（セッションライフサイクルとは何か）

セッションライフサイクルは次のフェーズで構成される。

1. **生成（Creation）**  
   - ログイン成功直後に Session ID / Token / Cookie が発行される
2. **維持（Maintenance）**  
   - 各リクエストで Cookie / Token が送信され続ける  
   - 必要に応じて Session 延長 / Token 更新が行われる
3. **終了（Termination）**  
   - 明示ログアウト（/logout）  
   - タイムアウト（非操作時間・トークン有効期限）  
   - デバイス変更・パスワード変更による失効  

### 3.1 Cookie ベースの場合

- Session ID が Cookie として保持される  
- ログアウト時に `Set-Cookie` で `Max-Age=0` / `Expires` 過去日付  
- タイムアウトはサーバ側 Session Store（DB/メモリ）の期限管理

### 3.2 Token ベース（OAuth/OIDC 含む）の場合

- Access Token（短寿命）、Refresh Token（長寿命）  
- フロントが Token をストレージに保存  
- 有効期限切れ近くで Refresh Token による Token 更新 API を呼ぶ  
- ログアウト時は Token 無効化エンドポイントを呼ぶ／フロントから破棄する

### 3.3 SAML/SSO を挟む構成

- IdP での Session と SP での Session が **別々に存在**  
- SP 側のログアウトと IdP 側のログアウトが分かれる場合がある  
- Single Logout（SLO）が設定されていると、他サービスも巻き込んでログアウトされる

---

## 4. 実践手順（観察によるセッションライフサイクルの把握）

### 4.1 ログイン直前・直後の Cookie / Token 差分を記録する

1. STEP1/STEP2 で特定したログイン URL にアクセス  
2. 開発者ツールを開き、`Application` / `Storage` / `Cookies` を表示  
3. ログイン前の Cookie / LocalStorage を記録  
4. ログイン操作（実際の認証は行わず、画面遷移までの観察に留める場合でも可）  
5. ログイン後の変化を記録  

観察項目：

- 新しく増えた Cookie 名・Token 名  
- Cookie 属性（Secure / HttpOnly / SameSite / Domain / Path）  
- LocalStorage / SessionStorage に保存されるデータの有無  

---

### 4.2 一定時間放置後の挙動を確認（タイムアウト方向）

※ 実環境で行う場合、あくまで「画面上の動き」を見るための観察に留める。

1. ログイン後、何も操作せず一定時間放置する  
2. 再度ページをリロード / 遷移してみる  
3. 開発者ツールで以下を確認：  
   - セッションが保持されているか  
   - 401 / 403 / 302（ログイン画面へ）などのレスポンスコード  
   - Token 有効期限切れのエラーメッセージが出るか  

これにより、  
**セッション有効期限のイメージ（短い／長い／ほぼ無期限）が把握できる。**

---

### 4.3 ログアウト処理の挙動を観察

ログアウト URL 例（STEP1 の情報から抽出）：

~~~~
/logout
/signout
/auth/logout
/session/logout
~~~~

観察する内容：

- `GET` / `POST` どちらか  
- ログアウト後にどの URL に遷移するか（トップ / ログイン画面 / IdP）  
- Cookie の削除が行われているか（`Set-Cookie` で `Max-Age=0` になっているか）  
- Token ベースの場合、Token 無効化 API を呼んでいるか  

---

### 4.4 複数タブ・複数デバイスを想定した挙動の整理（概念レベル）

実際に複数デバイスで試すのではなく、  
ドキュメントや UI 表現から以下を読み取る：

- 「他のデバイスからログアウト」ボタンの有無  
- ログイン履歴ページの有無（IP / デバイスなどが出るか）  
- 「○台のデバイスがログイン中」と表示されるか  

これらから、  
**セッションがアカウント単位でどのように管理されているか** を推定する。

---

### 4.5 MFA を含むセッションの動き（STEP2-5 連携）

MFA が有効なサービスで以下を確認する：

- MFA 完了前後で Cookie / Token が変わるか  
- MFA のたびに新しい Session / Token が発行されるか  
- 再ログイン時に MFA をどの頻度で要求するか（毎回／新デバイスのみなど）  

これにより、  
**MFA が「どのレイヤーのセッション」に紐づいているか** が見えてくる。

---

## 5. 注意事項

- 実ユーザーアカウントで頻繁なログイン/ログアウト試行は行わない  
- Token や Cookie の内容を改ざん・再送信するなどの行為は本 STEP の範囲外とする  
- セッション管理の不備を見つけても、本 STEP では「挙動としての把握」に留める  
- 具体的な攻撃シナリオ・悪用の検討は、後の STEP（権限・攻撃チェーン）に回す  

---

## 6. 参考文献

- OWASP Session Management Cheat Sheet  
- OWASP ASVS V2 Authentication / V3 Session Management  
- NIST SP800-63 Digital Identity Guidelines  
- 各フレームワークの Session 管理ドキュメント（Laravel / Spring / Rails / Express 等）

<<<END>>>