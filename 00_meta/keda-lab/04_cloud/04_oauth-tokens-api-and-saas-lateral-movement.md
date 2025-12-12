<<<BEGIN>>>
# 04 STEP4-04 OAuth / トークン / API を用いたクラウド内部横展開

## 1. このSTEPのゴールと位置づけ

### 1-1. ゴール

この STEP4-04 のゴールは、

> **Azure AD（Entra ID）で取得したロール・権限を、  
> OAuth / トークン / API を通じて  
> M365・SaaS へ“横に展開”する具体像を理解すること**

です。

STEP4-03 までで、

- Azure AD 管理ロール
- Conditional Access / MFA の評価
- 管理操作が可能な足場

を得ました。

本 STEP では、そこから

> **「ログイン」ではなく「API 操作」で世界が広がる**

という、クラウド特有の横展開フェーズに入ります。

---

## 2. なぜ OAuth / トークンが重要なのか

### 2-1. クラウドにおける“実体”

オンプレ環境では：

- ユーザー
- マシン
- セッション

が攻撃の単位でした。

クラウドでは：

> **トークン（Access Token / Refresh Token）こそが実体**

になります。

- トークンを持つ者 = 権限を持つ者
- MFA / CA は
  - トークン取得時に評価されるが
  - **トークン使用時には再評価されない** ケースが多い

---

## 3. OAuth / OpenID Connect の最小整理（攻撃者向け）

### 3-1. 登場人物

- **Resource Owner**：ユーザー
- **Client**：アプリ（App Registration / Enterprise App）
- **Authorization Server**：Azure AD
- **Resource Server**：Microsoft Graph / 各種 SaaS API

### 3-2. 攻撃者が注目するポイント

- Client（アプリ）が
  - どの API 権限（Scope / Role）を持つか
- Token が
  - 誰名義で
  - どの権限を含むか
  - どれくらい有効か

---

## 4. App Registration / Enterprise App の攻撃面

### 4-1. なぜアプリが重要か

Azure AD では、

- アプリ = 権限の器
- ユーザーが MFA していなくても、
  - アプリが強い権限を持てば
  - API 操作は可能

という構造があります。

---

### 4-2. 攻撃者が狙う典型パターン

#### パターン① 強権限アプリの新規作成

前提：

- Application Administrator
- Cloud Application Administrator
- Global Administrator

攻撃の考え方：

1. 新しい App Registration を作成
2. Microsoft Graph などに
   - 高権限（例：`Directory.ReadWrite.All`, `Mail.Read`, `Files.Read.All`）
   を付与
3. 管理者同意（Admin Consent）
4. クライアントシークレット / 証明書を発行
5. **ユーザー操作なしで API アクセス**

---

#### パターン② 既存アプリの悪用

- 既存の業務アプリに
  - 過剰な API 権限が付与されている
- 管理が形骸化している

攻撃者視点：

- 新規作成よりも
  - **目立たない**
- トークン取得の痕跡が
  - 業務トラフィックに埋もれやすい

---

## 5. Microsoft Graph を使った横展開（概念）

### 5-1. Graph が見える世界

Microsoft Graph API からは：

- ユーザー / グループ
- メール（Exchange Online）
- ファイル（SharePoint / OneDrive）
- Teams / チャット
- デバイス情報
- 条件付きで管理設定

が **一貫した API** で操作可能。

---

### 5-2. 攻撃チェーン例（概念）

1. 強権限アプリから Access Token を取得
2. Graph API を用いて：
   - 全ユーザー一覧取得
   - 特定ユーザーのメール閲覧
   - SharePoint ドキュメント列挙
3. 取得情報から：
   - 機密データの奪取
   - 次の侵害対象（別 SaaS / API）を特定

ここでは **GUI 操作は一切不要**。

---

## 6. SaaS 連携を利用した二次横展開

### 6-1. SaaS が Azure AD を信頼している構図

多くの SaaS は：

- Azure AD を IdP として信頼
- SAML / OIDC でログイン

つまり：

> Azure AD トークン  
> ＝ SaaS への入場券

になります。

---

### 6-2. 攻撃者視点での横展開

- Azure AD 上の
  - ユーザー
  - アプリ
  - トークン
を利用し、

- Salesforce
- ServiceNow
- Box
- Confluence / Jira
などへ **連鎖的にアクセス**。

特に：

- 自動プロビジョニング
- グループ連携

がある場合、

- Azure AD 側グループ操作
  → SaaS 側権限操作

が成立する。

---

## 7. Persistence（永続化）としてのアプリ

### 7-1. なぜアプリは消されにくいか

- ユーザー退職時：
  - アカウントは無効化される
- アプリ：
  - 放置されやすい
  - 利用状況が見えにくい

攻撃者視点：

> **アプリはクラウド版バックドア**

---

### 7-2. 永続化パターン（概念）

- 強権限アプリを残す
- 証明書ベース認証（長寿命）
- 管理者以外は気づかない API 利用

TLPT では：

- 「作れるか」より
- **「検知できるか」**

が評価対象。

---

## 8. Conditional Access / MFA との関係整理

### 8-1. CA が効く場所・効かない場所

- 効く：
  - ユーザーの対話的サインイン
- 効きにくい：
  - アプリ認証
  - バックエンド API 操作

### 8-2. 攻撃者視点での本質

> MFA を守る  
> ＝ 人を守る  
> API を守る  
> ＝ アプリ・権限設計を守る

---

## 9. Blue / DFIR 視点（要点）

### 9-1. ログ

- Azure AD Audit Log
- Sign-in Log（App Sign-in 含む）
- Graph API アクセスログ

### 9-2. 見落とされやすい点

- App Registration の権限変化
- Admin Consent の履歴
- トークン取得頻度・パターン

---

## 10. STEP4 の総括と次への接続

### 10-1. STEP4 全体で到達した地点

- オンプレ AD 侵害
- Hybrid Identity 突破
- Azure AD 管理権限獲得
- OAuth / API によるクラウド内部横展開

これにより、

> **オンプレ → クラウド → SaaS**  
> という現代企業の実体を  
> 攻撃チェーンとして一気通貫で説明可能

になった。

---

## 11. 次の STEP（5-01）への接続

次の **05_pentest-chain** では、

- STEP1〜4 の成果を統合し、
- **実務ペンテスト / TLPT 用の  
  攻撃チェーン（シナリオ）として再構築**

します。

Recon から Cloud までを、

> 「どう攻め、どこまで行けるか」

という **一本のストーリー** に落とし込みます。

<<<END>>>