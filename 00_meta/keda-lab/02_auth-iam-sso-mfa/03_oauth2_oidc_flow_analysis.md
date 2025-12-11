<<<BEGIN>>>
# STEP2-3 OAuth2 / OIDC フローの理解と、STEP1 情報を用いた方式特定

本 STEP では、OAuth2 / OIDC（OpenID Connect）を  
**外観から正しく判定し、構造を理解し、後続の SSO・権限分析に接続できる状態** を作る。

OAuth/OIDC は現代システムで最も普及している認証方式であり、  
特にクラウド（Cognito / Azure AD / Auth0 / Okta）を利用する環境では必須知識である。

---

## 1. 目的

- OAuth2 / OIDC の基本フロー（AuthZ Code, Implicit, PKCE）の理解  
- 画面操作・URL・ヘッダから **OAuth/OIDC 利用を外観で判別** できるようにする  
- STEP1 で取得した URL・技術スタック・クラウド痕跡を利用し、認証方式を確定させる  
- 次の STEP（SAML/SSO・MFA）へつながる基盤を構築する  

---

## 2. 前ステップとの関係（STEP1 / STEP2-1 / STEP2-2 の成果をどう使うか）

### 2.1 STEP1-3（サブドメイン）
- `auth.<domain>`  
- `id.<domain>`  
- `login.<domain>`  
- 外部 IdP 方向へのリダイレクト  
これらから **OAuth/OIDC の候補 URL** を抽出する。

### 2.2 STEP1-4（技術スタック推定）
フロントに React / SPA が使われている場合、OAuth/OIDC の採用率が高い。  
JS 内に以下の痕跡があれば確度はさらに上がる。
- `client_id`
- `redirect_uri`
- `issuer`
- `openid`, `scope=openid`

### 2.3 STEP1-5（クラウド露出）
以下が見えればほぼ確実に OAuth/OIDC：
- Cognito: `https://<domain>.auth.<region>.amazoncognito.com/oauth2/authorize`
- AzureAD: `https://login.microsoftonline.com/<tenant>/oauth2/v2.0/authorize`
- Auth0: `https://<tenant>.auth0.com/authorize`
- Okta: `https://<tenant>.okta.com/oauth2/default/v1/authorize`

### 2.4 STEP1-6（GitHub・設定漏えい）
- `CLIENT_ID=xxxx`  
- `OIDC_ISSUER=https://...`  
- `REDIRECT_URI=https://example.com/callback`  
が露出していれば、方式判定に直結する。

### 2.5 STEP2-1 / STEP2-2 との連携
- Session/Cookie 方式でないことを外観で排除  
- 外部リダイレクト + パラメータを観察し、OAuth/OIDC の条件を満たすか判定

---

## 3. 情報源・手法の体系（OAuth/OIDC をどう見分けるか）

OAuth/OIDC を利用しているサイトは、ほぼ必ず以下の特徴を持つ。

### 3.1 リダイレクト（302）で IdP に遷移する
例：  

~~~~
https://auth.example.com/oauth2/authorize?
  response_type=code&
  client_id=abc123&
  redirect_uri=https://app.example.com/callback&
  scope=openid profile email
~~~~

### 3.2 URL に特有のパラメータが存在する
- `client_id=`  
- `redirect_uri=`  
- `response_type=code`  
- `scope=openid`  
- `state=`  
- `code_challenge=`（PKCE）  

### 3.3 Callback URL の存在
ログイン後、必ず以下のようなコールバックが発生する：

~~~~
https://app.example.com/callback?code=xxxx&state=yyyy
~~~~

### 3.4 Token 取得エンドポイントの存在
ブラウザ開発者ツールで以下が見える：

~~~~
POST /oauth2/token
grant_type=authorization_code
code=xxxx
redirect_uri=https://app.example.com/callback
~~~~

### 3.5 OIDC 固有の `/userinfo` API
~~~~
GET /oauth2/userInfo
Authorization: Bearer <access_token>
~~~~

以上の 3〜5 を確認できれば **OIDC を利用している可能性が高い**。

---

## 4. 実践手順（非侵襲・観察ベース）

### 4.1 STEP1 の URL 情報から “OAuth/OIDC らしき入り口” を特定

調査対象例：

~~~~
/authorize
/oauth2/authorize
/login/oauth2
/auth/callback
/sso/oidc
~~~~

これらは OAuth 系の典型 URL。

---

### 4.2 ブラウザの Network でログイン開始 → リダイレクトの観察

観察項目：

- リクエストが外部ドメインへ移動するか？（IdP 判定）  
- クエリに `response_type` `client_id` が含まれるか？  
- `code` 付きで戻ってくるか？（AuthZ Code Flow）  

リダイレクトの存在は Session 認証との最大の違い。

---

### 4.3 HTML / JS からフロント構成を推定（STEP1-4 連携）

SPA の場合：

- `authConfig = { clientId: "...", issuer: "...", redirectUri: "..." }`
- Auth0 SDK: `createAuth0Client(...)`
- Cognito JS SDK: `CognitoAuth(...)`

これらから、方式だけでなく **利用しているプロバイダ** まで推定できる。

---

### 4.4 Token 取得リクエストの存在を確認（開発者ツール）

例：

~~~~
POST https://auth.example.com/oauth2/token
grant_type=authorization_code
code=xxxx
~~~~

`grant_type=authorization_code` は OAuth の決定的証拠。

---

### 4.5 Id Token / Access Token の構造確認

レスポンス例：

~~~~
{
  "access_token": "eyJhbGciOi...",
  "id_token": "eyJraWQiOi...",
  "token_type": "Bearer"
}
~~~~

JWT の構造により：

- ヘッダ: 使用アルゴリズム  
- ペイロード: iss / sub / aud / exp が含まれる（OIDC 仕様）  

攻撃では解読しないが、**構造を観察し方式を特定することが目的**。

---

## 5. 注意事項

- IdP への認証やログイン試行は行わない  
- Token の解析はフロー理解のための構造観察に留める  
- OAuth/OIDC の弱点分析（リダイレクト操作・Token misuse 等）は後続の STEP で扱う  
- この STEP のゴールは **方式の判定** と **構造の理解** に限定する  

---

## 6. 参考文献

- OAuth 2.0 RFC 6749  
- OIDC Core Specification  
- NIST Digital Identity SP800-63  
- Auth0 / Azure AD / Amazon Cognito Docs  
- OWASP ASVS V2 Authentication  

<<<END>>>