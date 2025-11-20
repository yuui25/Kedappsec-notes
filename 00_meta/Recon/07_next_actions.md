# 07_Next_Actions (L3 Detailed Manual)

本ドキュメントは、Recon（01〜06）で取得した  
**Boundary / ID / Role / Tenant / Owner / API / Flow / State**  
の仮説をもとに、実際に深掘りする “次のアクション” を標準化する手順書。

ここでは、テストの優先順位、実行順序、定型的に行うべき検証を  
完全に再現可能な形で定義する。

---

# 目的
- Recon で作成した「仮説」を実際のテストケースに変換  
- 権限不備（BAC）の検証を高速に実行  
- Flow/Wizard/State の破綻点を確定  
- Hidden API / Cloud misconfig の実行検証  
- 診断業務の品質を一定にするための標準手順化  

---

# 手順一覧（Overview）
1. IDOR（list/detail/update/delete）検証  
2. tenant/org 境界破り検証  
3. role/permission 操作の検証  
4. ownership（ownerId）書換の検証  
5. Hidden API の direct-call 検証  
6. Flow/Wizard Bypass の検証  
7. State Machine 遷移の検証  
8. Session/Token Lifecycle の検証  
9. Cloud misconfig → Backend 認可検証  
10. UI vs API Gap の踏み込み  
11. 重要点のエビデンス化（証跡確定）  
12. 報告書への統合  

---

# 詳細手順

---

## 1. IDOR（list/detail/update/delete）検証（最優先）

### 手順
1. Findings/Hypothesis で抽出された IDOR 候補を全て列挙  
2. 以下の順番でテストする：

#### （A）list → 他者ID混在の検証  
- /list, /search, /all の結果に “自分とは異なる ID” が含まれるか確認  

#### （B）detail → ID切替  
- /resource/{id} を自ID → 他ID に変更する  

#### （C）update/delete → ID切替  
- payload.itemId を自ID → 他ID に変更  
- DELETE /resource/{id} を他IDに  

### 判定
- 200/204 → ほぼ確定  
- 403/404 → server-side validation あり（他の箇所を調査）  

---

## 2. tenant/org 境界破り検証（SaaS案件で最重要）

### 手順
1. tenantId/orgId を含む API を抽出  
2. 以下のテストを順番に行う：

#### テストA：tenantId を削除  
- パラメータ/ヘッダから tenantId を削除してレスポンスを見る  

#### テストB：tenantId を他組織の値に変更  
- レスポンスや JS から得た他 tenantId をセット  

#### テストC：tenantId を NULL/空文字  
- fallback で他テナントや global tenant に落ち込む場合がある  

### 当たりポイント
- 他組織のデータが返る  
- global default tenant に落ちる  
- 予期せぬ filter が外れる  

---

## 3. role/permission 操作の検証

### 手順
1. role, scope, permission, isAdmin を含む API を抽出  
2. update/create API に role パラメータを追加  
3. UI ではできない role 変更を API で直接試す  

### 当たりパターン
- role=admin が通る  
- permission=["manage_users"] が受け付けられる  
- role-check が存在しない  

---

## 4. ownership（ownerId）書換検証

### 手順
1. ownerId/createdBy を含む API を確認  
2. update/create の payload 内 ownerId を他ユーザIDに変更  
3. 読み取り系 API の ownerId を変えてアクセス  

### 当たりポイント
- 他ユーザの item を「自分のもの」として操作可能  
- cross-account の書換が可能  

---

## 5. Hidden API の direct-call 検証

### 対象
- /admin/**  
- /internal/**  
- /debug/**  
- /preview/**  
- /bulk/**  

### 手順
1. JS/API Mapping で抽出した hidden API をリスト化  
2. 必要な header/token を最小限にして direct-call  
3. 権限チェックの有無を確認  

---

## 6. Flow/Wizard Bypass の検証

### 対象
- /step-1 → /step-2 → /confirm → /finish  
- multi-step forms  
- onboarding flows  

### 手順
1. 各 step URL を直アクセス  
2. セッション破壊 + confirm/finish 直アクセス  
3. hidden フィールドの値改変 + confirm  

### 当たりポイント
- 完了画面が開く  
- フローがショートカットされる  
- 値を改変しても backend validation がない  

---

## 7. State Machine 遷移の検証

### 対象
- draft / published / archived  
- pending / approved / rejected  
- status=1/2/3  

### 手順
1. 現在状態を確認  
2. UI 不可能な遷移を API で直接叩く  
3. 状態が変わるか確認  

---

## 8. Session/Token Lifecycle の検証

### 手順
1. refresh token 有無  
2. session 再ログイン挙動  
3. token rotation の有無  

### 当たりポイント
- 無期限 + rotation なし  
- multiple session bypass  
- flow bypass 混入  

---

## 9. Cloud misconfig → Backend 認可検証

### 手順
1. dev/staging backend が public か確認  
2. preview API の認可を確認  
3. CORS misconfig により unauthorized access が通るか  

---

## 10. UI vs API Gap の踏み込み（強力）

### 手順
1. UI にない機能が API に存在 → 必ず direct-call  
2. UI が制限する機能（role/tenant）を API で変更  
3. UI の表示だけで権限が判断されていないか確認  

---

## 11. 重要点のエビデンス化（証跡作成）

### 証跡に必要な情報
- リクエスト  
- レスポンス  
- ID の変化  
- tenant/role/owner の変化  
- 実画面（UI との差分）  
- 再現手順  
- 影響範囲  

---

## 12. 報告書への統合
Findings/Hypothesis で作成した論理構造を  
そのまま報告書の “脆弱性概要” と “原因/影響” に変換可能。

---

# 出力テンプレ（Kedappsec-notes 用）
~~~~
## Next Actions Summary

### IDOR Tests
- list:
- detail:
- update:
- delete:
- notes:

### Tenant/Org Boundary Tests
- remove tenantId:
- change tenantId:
- null tenantId:
- notes:

### Role/Permission Tests
- role param:
- scope/permission:
- admin traces:
- notes:

### Ownership Tests
- ownerId change:
- createdBy:
- notes:

### Hidden API Tests
- internal:
- admin:
- preview:
- debug:
- notes:

### Flow/Wizard Tests
- step access:
- confirm/finish:
- session destroy:
- notes:

### State Machine Tests
- transitions:
- forbidden transitions:
- notes:

### Session/Token Tests
- rotation:
- refresh:
- multi-session:
- notes:

### Cloud Misconfig Tests
- dev/staging:
- CORS:
- preview backend:
- notes:

### UI vs API Gap Tests
- missing UI:
- hidden CRUD:
- tenant/role:
- notes:

### Final Evidence Requirements
- request:
- response:
- screenshots:
- explanation:
- impact:
~~~~

---