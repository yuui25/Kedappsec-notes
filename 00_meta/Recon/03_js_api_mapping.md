# 03_JS_API_Mapping (L3 Detailed Manual)

本ドキュメントは、L3 Blackbox Recon の中心ともいえる  
**JavaScript 解析 → API 構造復元 → 権限境界推論**  
を再現性高く実施するための手順書である。

Tech Fingerprinting → Attack Surface で下地を固めたうえで、  
このフェーズで “認可モデルの実像” を逆算していく。

---

# 目的
- JS から fetch/XHR を網羅抽出  
- 隠し API / 管理 API を特定  
- API の CRUD/役割/境界を推論  
- ID（owner/tenant/account） の意味付け  
- “UIにない機能” の検出  
- 権限不備（BAC）を当てるための事前仮説生成  

---

# 手順一覧（Overview）
1. JS の取得と分類  
2. fetch/XHR の網羅抽出  
3. API Request/Response パターンの整理  
4. ID/Role/Tenant/Owner の意味推論  
5. Function / Feature 単位での API グルーピング  
6. UI 非依存（Hidden API）の抽出  
7. CRUD/MUTATION の分類  
8. Direct-call が可能か判定（前提条件チェック方式）  
9. Access Control カバレッジ推論（UI vs API）  
10. BAC 初期仮説の生成  

---

# 詳細手順

---

## 1. JS の取得と分類

### 手順
1. Network → JS タブから全JSを保存  
2. ファイル名・chunk単位で以下に分類：

### 分類基準
- vendor（ライブラリ群）  
- main/app（アプリ本体）  
- feature modules（画面/機能単位）  
- config/env（環境変数）  
- lazy load / dynamic import（ページ固有）  

### 目的
- どのJSがどの機能グループを担当しているか推理  
- fetch/XHR の探索範囲を絞る  

---

## 2. fetch/XHR の網羅抽出

### 手順
1. 各 JS で以下を検索：
   - fetch(
   - axios(
   - $.ajax(
   - XMLHttpRequest  
   - request(  
   - client.query(（GraphQL）  
2. **API の生 URL** を列挙  
3. **ヘッダ処理** の共通関数を探す（auth token, session, csrf）  
4. **Response ハンドリング** を読み “重要フィールド“ をメモ  
　（userId, tenantId, orgId, role, ownerId など）

### 目的
- UI が呼ぶ API の全容把握  
- 隠しAPI検出の準備  

---

## 3. API Request/Response パターンの整理

### Request パターン
- GET（list/detail）  
- POST（create/register）  
- PUT/PATCH（update）  
- DELETE（remove）  

### Response パターン
- data → resource  
- items → list  
- meta → pagination  
- errors → バリデーション  

### 目的
- API の CRUD 性質を分類し、後の BAC 推論の基礎を固める  

---

## 4. ID/Role/Tenant/Owner の意味推論（最重要）

### 手順
1. API の Request/Response 内にある ID系項目を全抽出  
2. 以下のどれに該当するか分類：

### ID分類（BACの核）
- **Subject ID（操作主体）**: userId / accountId  
- **Boundary ID（組織境界）**: tenantId / orgId  
- **Resource ID（対象リソース）**: itemId / postId / recordId  
- **Ownership ID（所有者）**: ownerId / createdBy  
- **Role ID（権限グループ）**: roleId / permissionName  

### 推論ポイント
- tenantId = 組織境界で最も重要  
- ownerId = “自分以外のリソース” チェックの基準  
- role は UI と API の差分を探す（最重要）  

---

## 5. Function / Feature 単位での API グルーピング

### 例
- Auth：/login /auth/refresh  
- User：/user/{id}/  
- Tenant/Org：/organization/**  
- Billing：/plan /subscription  
- Items：/item/list /item/update  
- Settings：/profile /config  

### 目的
- “どの機能のどこで境界が破れるか” を明確化  
- Hidden API を視覚化  

---

## 6. UI 非依存（Hidden API）の抽出

### 手順
1. JS には存在 → UI からは呼ばれない API を抽出  
2. 条件付き呼び出し（if(admin) …） を探す  
3. Preview / Draft / Batch API を探す  

### 高確率で当たりの領域
- /internal/  
- /admin/  
- /debug/  
- /preview/  
- /bulk/**  

### 目的
- **UI の制御を経ない権限バイパス** の発見  

---

## 7. CRUD / MUTATION の分類（BACに直結）

### CRUD分類
- Read-only（list/detail）  
- Write（update/create/delete）  

### MUTATION分類
- Bulk update  
- import/export  
- 状態変更（status, activate, approve）  

### 観点
- “UIには存在しないWrite API” は権限不備の最有力候補  

---

## 8. Direct-call の可能性判定（前提条件検証）

### 手順
API が以下を必要とするか確認：
- CSRF token  
- 特定 header（x-app-id 等）  
- state/nonce  
- UI で事前取得したID（wizard系）

### 判定観点
- **前提条件がない API = 直接叩ける API**  
- 前提の弱い API は権限不備候補  

---

## 9. Access Control カバレッジ推論（UI vs API）

### UI 側
- ボタン/アイコンが表示されない  
- 編集/削除ができない  

### API 側
- update/delete が存在する  
- role を操作できる  
- tenantId が固定されていない  

### 推論式
UI と API の機能差分 = 権限不備候補

---

## 10. BAC 初期仮説生成（この段階で候補を作る）

### 例
- “tenantId が固定されないので他組織のデータ参照の可能性”  
- “role パラメータがAPIに残っているので role escalation の可能性”  
- “UI にない delete API が存在する”  
- “ownerId を書き換えられる構造になっている”  
- “一覧APIに他ユーザの itemId が混ざっている”  

### 目的
この仮説を次章「BAC/Logic Candidates」で検証して当てにいく。

---

# 出力テンプレ（Kedappsec-notes 用）
~~~~
## JS → API Mapping Summary
- JS Groups:
- fetch/XHR Endpoints:
- Headers/Auth Logic:
- ID Structure (subject/boundary/resource/owner/role):
- Feature-based API Groups:
- Hidden APIs:
- CRUD/MUTATION Structure:
- Direct-call Feasibility:
- UI vs API Gap:
- BAC Hypothesis:
- Notes:
~~~~

---