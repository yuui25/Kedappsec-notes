# 05_BAC_Logic_Candidates (L3 Detailed Manual)

本ドキュメントは、L3 Blackbox Recon の最終目的である  
**BAC（Broken Access Control）および Logic Flaw を最短で特定する**  
ための「推論エンジン」として機能する詳細手順である。

Tech Fingerprinting → Attack Surface → JS/API Mapping → Cloud Misconfig  
で集めた全情報を基に、  
**“どこに権限境界が存在し、どこが破れる可能性が高いか”**  
を論理的にモデル化し、実際のテスト候補へ落とし込む。

---

# 目的
- 権限境界（Boundary）を構造化して把握  
- ID/Resource/Role/Tenant/Owner の正しい関係を推定  
- UI と API の不整合から弱点を抽出  
- Flow/Wizard の突破点を推測  
- 他者IDアクセスの可能性を整理  
- Logic Flaw（ビジネスロジック）をモデル化  
- **“優先すべき当たりポイント” を抽出**  

---

# 手順一覧（Overview）
1. 権限境界モデル（Boundary Model）の構築  
2. ID 構造（subject/boundary/resource/owner）の推論  
3. API Capability（操作可能性）マトリクスの作成  
4. UI vs API の乖離点抽出（差分分析）  
5. IDOR 候補の生成（ID切替テストの優先度付け）  
6. Role Escalation の候補作成  
7. Tenant/Org 境界破り候補  
8. Flow/Wizard ロジックの突破候補  
9. Hidden API / Admin API の利用可能性分析  
10. 状態遷移（State Machine）推論  
11. “最重要 BAC/Logic 当たり” の作成  

---

# 詳細手順

---

## 1. 権限境界モデル（Boundary Model）の構築
まず **認証後の境界構造をモデル化** する。

### 対象
- user（主体）  
- role（権限ベース）  
- tenant / organization（組織境界）  
- items / resources（対象）  
- ownership（所有権）  

### 典型構造例（SaaS系）
User → Role → Tenant → Resource → Owner

### 手順
1. JS/API Mapping で抽出した ID を分類  
2. 次の 3つの境界を明確化：

#### （1）Subject Boundary
- userId / accountId

#### （2）Tenant Boundary（最重要）
- tenantId / orgId / workspaceId

#### （3）Resource Ownership Boundary
- item.ownerId  
- createdBy, updatedBy  

---

## 2. ID 構造（subject/boundary/resource/owner）の推論

### 手順
1. 各 API の Request/Response の ID を分類  
2. 以下3点を必ず記録：

### （A）Subject（操作主体）
- userId  
- accountId  

### （B）Boundary（組織境界）
- tenantId  
- orgId  

### （C）Ownership（リソース所有権）
- ownerId  
- createdBy  

### 観点
これを明確にすると、  
**“どこを書き換えれば他者リソースに触れるか”** が分かる。

---

## 3. API Capability（操作可能性）マトリクスの作成

すべての API を **CRUD 能力ベース** で分類する。

### 例
| API | Read | Create | Update | Delete | Notes |
|-----|------|--------|--------|--------|-------|
| /user/me | ○ | - | △ | - | 自分のみ |
| /item | ○ | ○ | ○ | ○ | tenant 依存 |
| /admin/user | ○ | ○ | ○ | ○ | admin only? |

### 目的
- UI にない “Update/Delete” が見つかる  
- role/tenant 境界の有無を推論可能  

---

## 4. UI vs API の乖離点抽出（差分分析）

最重要フェーズのひとつ。

### 手順
1. UI に存在する機能一覧  
2. API に存在する機能一覧  
3. 両者の差分を算出：

### 当たりポイント
- UI には Delete ボタンがない → API に delete がある  
- UI には role 変更ができない → API には role がある  
- UI は tenant 固定 → API は tenantId を受け取る  
- UI では owner 固定 → API は ownerId を書き換え可能  

### 目的
100% BAC の最短ルート。

---

## 5. IDOR 候補の生成（ID切替テストの優先度付け）

### 手順
1. Resource API の IDフィールドを列挙  
2. 切り替え候補一覧を作成：

#### 例
- GET /item/123 → 他ID 124 に変更  
- POST /item/update → payload.itemId を変更  
- /download?fileId=111 → 112  

### 優先順位の決め方（絶対ルール）
- list → detail → update  
- update/delete が最優先  
- ownerId があるなら最優先  
- tenantId が可変なら即テスト  

---

## 6. Role Escalation の候補作成

### パターン抽出
- API に role が存在する（role=admin）  
- isAdmin=true が API で受け取られる  
- UI 隠し機能（JS で admin 用分岐）  
- 残骸管理API（/admin/**）

### 目的
- 開発者が role-check を書き忘れる箇所を発見  
- UI 側に role 制御を依存しているケースを浮かび上がらせる  

---

## 7. Tenant/Org 境界破り候補

SaaS で最も高い報奨金に直結する領域。

### 手順
1. tenantId を含む API を抽出  
2. 以下のどれかが当たれば高確率で主脆弱性：

### 当たりパターン
- tenantId が UI から見えないのに API にある  
- tenantId がレスポンスに毎回含まれる  
- tenantId = 任意値を受け入れる  
- tenantId を省略すると “外部のデータ” が返る  
- list API の結果に他tenantのデータ混在  

---

## 8. Flow/Wizard ロジックの突破候補

### 手順
1. /step-1 → /step-2 → /confirm → /finish の URL 遷移記録  
2. セッション破壊 → 直打ち  
3. 値書換 → confirm直打ち  
4. ログアウト → 完了画面直打ち

### 当たりポイント
- 完了画面が直接開く  
- confirm で値改変しても backend が未検証  
- 前提パラメータ（hidden）が無条件通過  

---

## 9. Hidden API / Admin API の利用可能性分析

### 手順
1. JS でのみ存在し UI 非表示の API を列挙  
2. /internal /admin /debug /preview を直接叩く  
3. role-check が存在しない API を探す  

### 当たりパターン
- preview API で実データ取得  
- /admin/list → 200  
- dev/staging backend が public（Cloud misconfig と結合）  

---

## 10. 状態遷移（State Machine）推論

### 手順
1. リソースの状態（draft/published/archived）を抽出  
2. “変更できないはずの状態遷移” を特定  
3. update API のペイロードで無理やり変更してみる  

### 例
- draft → published が制限されているのに API では自由  
- approve/reject が UI にないが API にある  

---

## 11. “最重要 BAC/Logic 当たり” を作成（総まとめ）

以下の 6 分野を必ず作る：

1. **IDOR Detail/Update/Delete**  
2. **tenantId / orgId の変更テスト**  
3. **role / permission の書換テスト**  
4. **ownerId / createdBy 書換テスト**  
5. **flow bypass（confirm/finish直打ち）**  
6. **hidden API direct access**  

---

# 出力テンプレ（Kedappsec-notes 用）
~~~~
## BAC / Logic Flaw Candidate Summary
### Boundary Model:
- Subject:
- Tenant/Org:
- Ownership:
- Resource IDs:

### UI vs API Gap:
- Hidden CRUD:
- Role/Permission Fields:
- Tenant Behavior:
- Owner Behavior:

### IDOR Candidates:
- List:
- Detail:
- Update/Delete:

### Role Escalation Candidates:
- role param:
- isAdmin:
- hidden admin API:

### Tenant/Org Break Candidates:
- tenantId pattern:
- orgId pattern:
- cross-tenant indication:

### Flow/Wizard Bypass Candidates:
- step URLs:
- confirm/finish bypass:
- session-destroy behavior:

### Hidden API / Admin API:
- internal:
- admin:
- debug/preview:

### State Machine Weakness:
- allowed transitions:
- missing server validation:

### Priority Targets:
1.
2.
3.
4.
5.
6.

~~~~

---
