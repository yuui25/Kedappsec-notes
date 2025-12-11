<<<BEGIN>>>
# STEP2-4 SAML / Enterprise SSO の構造理解と、STEP1 情報を用いた方式特定

本 STEP では、企業システムで広く採用されている  
**SAML 2.0 ベースの SSO（Single Sign-On）** を観察ベースで判定し、  
IdP / SP の構造、フロー、特徴を体系的に理解する。

SAML は OAuth/OIDC とは異なり XML を用いた企業向け SSO 方式であり、  
Azure AD / ADFS / Okta / OneLogin などで広く利用される。

---

## 1. 目的

- SAML の構造（IdP / SP / Assertion）の理解  
- 外観（POST, XML, RelayState, Redirect）から SAML を判定できるようになる  
- STEP1 により得た URL・クラウド資産から IdP の候補を特定する  
- 以降の IAM / SSO 深掘り（STEP2-5, STEP3 の AD 連携）につなげる  
- SAML の "動き" を正しく理解し、攻撃チェーン（TLPT）へ接続する基礎をつくる  

---

## 2. 前ステップとの関係（STEP1 / STEP2-1〜3 の成果をどう使うか）

### 2.1 STEP1-3 サブドメイン列挙
以下のような URL が存在すると SAML/SSO の可能性が高い：

- `idp.<domain>`  
- `sso.<domain>`  
- `login.<domain>`  
- `federation.<domain>`  
- `/adfs/ls/`（ADFS）  

### 2.2 STEP1-4 技術スタック推定
- 企業向けポータル（SharePoint, ASP.NET） → SAML が多い  
- Java 系 IdP（Shibboleth）の痕跡 → SAML 利用濃厚

### 2.3 STEP1-5 クラウド露出資産
- Azure AD → `login.microsoftonline.com`  
- Okta → `<tenant>.okta.com`  
- OneLogin → `<tenant>.onelogin.com`  

これらは高確度で SAML または OIDC を利用している。

### 2.4 STEP1-6 公開リポジトリ
以下が露出していた場合：

- `SAML_METADATA.xml`  
- `ENTITY_ID=`  
- `ASSERTION_CONSUMER_SERVICE_URL=`  

SAML 利用がほぼ確定。

### 2.5 STEP2-1〜3 との連携
Session 認証・OAuth/OIDC と **挙動が明確に異なる部分** を確認し、  
誤判定を避ける。

---

## 3. 情報源・手法の体系（SAML をどう見分けるか）

### 3.1 SAML の基本構造

SAML は以下の 3 要素で構成される：

- **IdP（Identity Provider）**：Azure AD / ADFS / Okta  
- **SP（Service Provider）**：ログインを提供するアプリ側  
- **Assertion**：IdP が発行する XML 形式の認証情報  

フローは以下のように動く：

~~~~
① ユーザーが SP にアクセス  
② SP → IdP へ SAMLRequest を送る  
③ IdP が認証  
④ IdP → SP へ SAMLResponse（XML）を返す  
⑤ SP が Assertion の署名を確認しセッション成立
~~~~

---

### 3.2 特徴的なパラメータ・挙動

SAML を利用している場合、以下の特徴が必ず現れる：

#### 特徴1：`SAMLRequest` または `SAMLResponse` が URL/POST に登場
例：

~~~~
https://idp.example.com/saml?SAMLRequest=...
~~~~

または、ログイン後に **巨大な Base64 XML** を含むフォーム POST：

~~~~
<form method="post" action="https://sp.example.com/acs">
  <input type="hidden" name="SAMLResponse" value="PHNhbWxwOk...">
  <input type="hidden" name="RelayState" value="xyz123">
</form>
~~~~

#### 特徴2：RelayState パラメータ
遷移先を識別するための値。

~~~~
RelayState=https://app.example.com/dashboard
~~~~

#### 特徴3：HTTP-POST + 自動送信フォーム
SAMLResponse を SP に送る際、多くのシステムで  
JavaScript による auto-submit が利用される。

#### 特徴4：XML 署名が含まれる
観察するだけでよく、分析は後工程で行わない。

---

## 4. 実践手順（非侵襲・観察ベース）

### 4.1 STEP1 の URL 情報から IdP / SP らしき場所を判断

~~~~
/saml
/sso
/idp
/adfs/ls/
/federation
~~~~

これらを優先調査対象とする。

---

### 4.2 Network タブでログイン開始 → リダイレクト経路を確認

観察ポイント：

- SP から IdP（外部 or 内部）へ遷移しているか  
- URL に `SAMLRequest=` が含まれるか  
- 戻りの POST に `SAMLResponse=` が含まれるか  

OAuth/OIDC と異なり、`client_id` や `code` は登場しない。

---

### 4.3 HTML の auto-submit フォームを確認

SAMLResponse を送る典型例：

~~~~
<html>
  <body onload="document.forms[0].submit()">
    <form method="post" action="https://app.example.com/acs">
      <input type="hidden" name="SAMLResponse" value="Base64のXML">
      <input type="hidden" name="RelayState" value="abc123">
    </form>
  </body>
</html>
~~~~

見つけた瞬間、SAML 利用が確定。

---

### 4.4 どのクラウド/IdP を利用しているか推測（STEP1-5 との連動）

URLから判定：

- Azure AD → `https://login.microsoftonline.com/.../saml2`  
- Okta → `https://<tenant>.okta.com/app/.../sso/saml`  
- OneLogin → `/trust/saml2/http-post/sso/`  
- ADFS → `/adfs/ls/`  

技術スタックとクラウド痕跡が結びつくことで  
**裏側の認証基盤が可視化される**。

---

### 4.5 フローの全体像を把握し、後工程につなげる

観察結果から：

- IdP はどこか  
- SP はどのアプリか  
- RelayState に何が入っているか  
- POST の転送先（ACS URL）はどこか  

これらは、

- STEP2-6（セッション管理）  
- STEP3（AD/Kerberos/横展開）  
- STEP4（クラウド ID 連携）  
- STEP5（TLPT シナリオ）

へ直接つながる重要情報となる。

---

## 5. 注意事項

- SAMLResponse の XML を解析しない（観察のみ）  
- ログイン試行・認証操作は行わない  
- IdP 側でのテナント情報推測などは禁止  
- 本 STEP はあくまで「方式とフローを外観から理解する」ためのもの  

---

## 6. 参考文献

- SAML 2.0 Core Specification  
- OASIS Security Services Technical Committee  
- Azure AD / Okta / OneLogin / ADFS SAML Docs  
- OWASP ASVS V2 Authentication  

<<<END>>>