<<<BEGIN>>>
# STEP2-8 認証フェーズ総まとめと STEP3（AD / 横展開）への橋渡し

本 STEP では、STEP2-1〜2-7 までで整理してきた  
**認証方式・SSO・MFA・セッション管理・弱点カテゴリ** を総括し、  
次フェーズ（STEP3：AD / Kerberos / Lateral Movement）にどう接続していくかを整理する。

---

## 1. 目的

- STEP2 で扱った内容を「何ができる状態になっているか」という観点でまとめる  
- STEP1（ASM/Recon）と STEP2（Auth/IAM）を、1本の “認証構造マップ” として結びつける  
- 次の STEP3（AD / Kerberos / 横展開）の前提となる  
  「ID / セッション / SSO / クラウドID」の接続関係を明確にする  

---

## 2. 前ステップとの関係（STEP1 ＋ STEP2 の統合）

### 2.1 STEP1 で得たもの（外部資産レベル）

- ドメイン / サブドメイン一覧  
- 技術スタック（言語 / フレームワーク / WAF / CDN）  
- クラウド露出資産（S3 / CloudFront / WebApps / IdP 痕跡）  
- GitHub / SaaS / CI/CD から見える構成ヒント  
- 優先度付きの「要注目 URL」（/login / /auth / /sso / /api など）

### 2.2 STEP2 で得たもの（認証レベル）

- **STEP2-1**：  
  - Session / Token / OAuth / SAML / SSO / MFA という “認証方式全体マップ”  
- **STEP2-2**：  
  - Session/Cookie 認証の構造と Cookie 属性の観察ポイント  
- **STEP2-3**：  
  - OAuth/OIDC のフローと、`authorize` / `token` / callback の観察方法  
- **STEP2-4**：  
  - SAML / 企業向け SSO のフロー（IdP / SP / Assertion）  
- **STEP2-5**：  
  - MFA の方式分類（TOTP / Push / SMS / Hardware key）とフロー  
- **STEP2-6**：  
  - セッションライフサイクル（生成・維持・終了）とログアウト挙動の整理  
- **STEP2-7**：  
  - 認証まわりの高リスク弱点カテゴリ（どこが “入口” になりやすいか）

これらを合わせると、  
**「どの URL / サービスで、どんな認証・セッション・MFA・SSO が動いているか」** を  
地図として描ける状態になっている。

---

## 3. STEP2 の成果物（何が見えるようになったか）

### 3.1 認証構造ビュー

- サービスごとに：
  - 認証方式（Session / OAuth / SAML）  
  - 認証を担うドメイン（アプリ自身 or 専用 Auth サブドメイン or クラウド IdP）  
  - MFA の有無・方式  
  - セッションの保持方法（Cookie / Token）  
- これにより、  
  「ユーザーの ID がどこで作られ、どこまで伝播していくか」を可視化できる。

### 3.2 セッション・トラスト境界の把握

- どの Cookie / Token が “ログイン済み” を表しているか  
- どのドメイン・パスまでセッションが共有されているか  
- ログアウト・タイムアウト・再ログインの挙動を観察済み  

これらは **後の “権限・横移動・内部侵入フェーズ” で必須となる情報**。

### 3.3 高リスクポイントのラフマップ

- OAuth の `redirect_uri` / `state` / `scope`  
- SAML の ACS URL / RelayState / メタデータ  
- Session Cookie の Secure / HttpOnly / SameSite / Domain  
- MFA の導入ポイント（どの操作にはあるか・ないか）  

実際の攻撃詳細に踏み込まなくても、  
「どこを深掘りすべきか」の優先度が見えるようになっている。

---

## 4. STEP3（AD / Kerberos / Lateral）への橋渡し

STEP3 では、  
**「認証されたユーザーが内部でどう振る舞うか」  
「ID が内部システム（AD / Kerberos）とどう接続しているか」**  
を理解していく。

STEP2 で整理した情報は、STEP3 で次のように生きてくる。

### 4.1 SSO / SAML / OIDC と AD / Kerberos の関係

- Azure AD / ADFS / Okta などの IdP 痕跡は、  
  裏で **AD / Azure AD / ハイブリッド構成** が存在する可能性を示す。  
- SAML/OIDC で認証された ID が、  
  AD グループ / ロール / クレーム としてどのようにマッピングされるかを  
  STEP3 で具体化していく。  

→ STEP2 で把握した「誰が IdP なのか」が、  
  STEP3 での **ID / 権限の源泉** の理解につながる。

### 4.2 セッションとドメイン境界

- Session/Cookie がどのドメインまで共有されているか  
- 社内ポータル（SharePoint / 社内 Web）への SSO の有無  

これらは、  
**「どこから社内リソース（AD / ファイルサーバ / SMB）に繋がり得るか」** のヒントになる。

### 4.3 MFA の位置と横展開リスク

- MFA が IdP レベルか、アプリレベルか  
- 初回ログイン時のみなのか、重要操作ごとなのか  

これにより、  
STEP3 で扱う「横展開（Lateral Movement）」の現実的な難易度や、  
**“MFA を超えた後のセッションがどこまで横方向に効くか”** を考える土台ができる。

### 4.4 クラウド ID とオンプレ AD の橋渡し

- STEP1-5 / STEP2-3 / STEP2-4 で見えた  
  Azure AD / AWS IAM / Cognito / Okta などのクラウド ID 情報は、  
  STEP3 で扱う **オンプレ AD / Kerberos / SMB / LDAP** と  
  どこで紐づいているかを考える出発点になる。

---

## 5. 次にやること（STEP3 の入り口）

STEP3-1 以降では、次の流れで理解を深めていく想定とする：

1. **STEP3-1：AD ドメインとディレクトリサービスの役割整理**  
   - ユーザー / グループ / コンピュータ / ポリシー  
   - STEP2 で見えた「アプリのユーザー ID」との関係を対応づける  

2. **STEP3-2：Kerberos チケットの基本と保存場所のイメージ**  
   - TGT / Service Ticket  
   - 「認証後の ID 情報」がどのようにキャッシュされるか  

3. **STEP3-3：社内サービス（SMB / RDP / LDAP 等）との関連づけ**  
   - STEP1 でのポート・サービス情報がここで意味を持つ  

4. **STEP3-4 以降：横展開の一般的な流れ（概念レベル）**  
   - 認証済み ID がどのように別ホスト・別サービスへ届くか  

このとき、  
**STEP2 で構築した「認証・セッション・SSO マップ」が前提情報として必須**になる。

---

## 6. 参考文献

- OWASP ASVS V2 Authentication / V3 Session Management  
- NIST SP800-63 Digital Identity Guidelines  
- Azure AD / ADFS / Okta / Auth0 / Cognito など各 IdP ドキュメント  
- 一般的な AD / Kerberos / Identity Federation 解説資料  

<<<END>>>