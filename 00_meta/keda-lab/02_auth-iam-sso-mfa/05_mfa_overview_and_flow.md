<<<BEGIN>>>
# STEP2-5 MFA（多要素認証）の方式分類とフロー観察

本 STEP では、MFA（Multi-Factor Authentication）の  
**方式分類・フローの構造・観察ポイント** を整理し、  
STEP1（外部資産 / URL / クラウド情報）および STEP2-1〜4（認証方式分類）の結果から、  
対象サービスがどの MFA を採用しているかを外観から判断できるようにする。

MFA は現代の Web・企業環境でほぼ必須となっており、  
実務の PT/TLPT では「どのフェーズでどんな MFA が存在し、どこに弱点が入るか」を理解するための基盤となる。

---

## 1. 目的

- MFA の主要方式（TOTP / Push / SMS / Hardware key）を体系化  
- 認証時にブラウザがどのように動くかを観察できるようにする  
- STEP1 の URL・技術スタック・クラウド情報から MFA の候補を推定する  
- 次 STEP（セッション管理・MFA バイパス観点の整理）へつながる基礎を構築する  

---

## 2. 前ステップとの関係（STEP1〜STEP2 の情報をどう使うか）

### 使用する STEP1 の情報

#### 2.1 STEP1-3 サブドメイン
- `/mfa`  
- `/challenge`  
- `auth.<domain>` 内の追加認証 API  
などの存在を対象候補にする。

#### 2.2 STEP1-4 技術スタック推定
- SPA や大規模 ID 基盤を使うサービスは Push/TOTP が多い  
- Laravel/Spring 等古めの構成では TOTP か SMS のことが多い

#### 2.3 STEP1-5 クラウド露出資産
クラウドサービスごとに MFA の種類が異なる：

- AWS Cognito → TOTP / SMS  
- Azure AD → Authenticator Push / FIDO2 / TOTP  
- Okta → Push / TOTP / WebAuthn  
- Auth0 → Guardian Push / TOTP  

### 使用する STEP2 の情報
- Session 認証 → MFA 前後の Cookie 変化を観察  
- OAuth/OIDC → MFA フローは IdP 側で完結  
- SAML → 二段階のリダイレクト → MFA 画面 → Assertion  

これらの違いを理解することで **MFA の位置（SP か IdP か）** を判定できる。

---

## 3. 情報源・手法の体系（MFA の種類と構造）

MFA は大きく次の 4 種類に分類される。

---

### 3.1 TOTP（Google Authenticator など）
特徴：
- 6桁コードを入力  
- `/mfa/code` `/verify` `/totp` のような API を伴う  
- 認証アプリと共有された secret で生成

観察ポイント：
- HTML フォームでコード入力欄が出る  
- Network に `/mfa/verify` が見える  
- Token/OIDC の場合は `/authorize` の途中で MFA 画面へ遷移

---

### 3.2 Push 通知（Azure AD / Okta / Auth0）
特徴：
- スマホアプリに「承認/拒否」  
- リアルタイム通知のため backend がポーリングすることが多い  

観察ポイント：
- Network に「数秒ごとに同一 API を叩く」挙動  
  例：`/mfa/status`, `/challenge/poll`  
- レスポンスに `pending`, `approved`, `denied` などが含まれる

---

### 3.3 SMS コード
特徴：
- 電話番号登録  
- 6 桁のコード  
- TOTP に似ているが、送信が SMS

観察ポイント：
- `/sms/send` `/sms/verify`  
- API のレスポンスに `destination: +81...` などが含まれる

---

### 3.4 Hardware Key / WebAuthn（FIDO2）
特徴：
- セキュリティキー（YubiKey 等）  
- ブラウザの認証 API を使用  

観察ポイント：
- JS で `navigator.credentials.create`  
- `webauthn`, `fido2` の文字列が JS 内に含まれる  
- フォーム POST ではなく Web API 呼び出し

---

## 4. 実践手順（非侵襲・観察ベース）

### 4.1 STEP1 の URL とステップ2の方式分類から “MFA 入口” を特定

候補例：

~~~~
/mfa
/challenge
/second-factor
/totp
/sms/verify
/oauth2/authorize?mfa_required=true
~~~~

IdP 側の MFA の場合、OAuth/OIDC の途中で MFA に飛ばされる。

---

### 4.2 Network で「MFA フローの開始 → 認証までの API」を観察

観察項目：

- どの時点で MFA が要求されるか  
  （初回認証後／毎回／リスクベース）  
- API の種類（verify/poll/send）  
- Token/Cookie の変化  
- リダイレクトが発生するか（OAuth 系）  

---

### 4.3 HTML / JS から方式を逆算

- Input フォーム → TOTP  
- Polling → Push  
- JS に WebAuthn API → Hardware key  
- SMS 送信メッセージ → SMS  

---

### 4.4 クラウドプロバイダ痕跡から方式を確定（STEP1-5 の連携）

例：  
- Azure AD の場合、Push + FIDO2  
- Cognito は SMS/TOTP  
- Auth0 は Guardian Push + TOTP  
- Okta は複数方式混在（Push/OTP/SMS/WebAuthn）

これにより、方式だけでなく **管理者がどのプロバイダを設定しているか** まで推定可能。

---

## 5. 注意事項

- MFA バイパスや攻撃手法は後の工程（STEP5）で扱うため、本 STEP では行わない  
- MFA の設定状況は観察ベースで判定するに留める  
- 実際のコード送信や認証操作は行わない  
- 本 STEP は「認証方式一覧 → MFA」という流れを体系化することが目的  

---

## 6. 参考文献

- NIST SP800-63B MFA Requirements  
- OWASP ASVS V2 Authentication  
- FIDO2 / WebAuthn Specs  
- Azure AD / Amazon Cognito / Okta / Auth0 Documentation  

<<<END>>>