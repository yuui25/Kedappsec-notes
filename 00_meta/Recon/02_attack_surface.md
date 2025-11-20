# 02_Attack_Surface_Mapping (L3 Detailed Manual)

本ドキュメントは、L3 Blackbox Recon の中核となる  
**Attack Surface Enumeration（攻撃面の全特定）** の実務手順を体系化したもの。

Tech Fingerprinting の結果を踏まえ、  
“どこが入口で、どこが境界で、どこに脆弱性が入り込む余地があるか” を  
**抜け漏れなく列挙する工程**。

---

# 目的
- 公開/非公開の URL・API を完全に洗い出す  
- UI に現れない API を特定する  
- Flow（wizard）や Export など高リスク部分を列挙  
- 後続の JS/API Mapping・BAC/Logic 推論の土台を作る  

---

# 手順一覧（Overview）
1. 公開URL・認証URLの探索  
2. JS ファイル一覧の収集  
3. UI遷移 → XHR/fetch を全捕捉  
4. API Path の分類（公開/認証/内部/管理）  
5. 機能軸での API グルーピング  
6. Hidden API / Admin API の探索  
7. File Upload / Download / Export の特定  
8. Batch / list / search 系の抽出  
9. GraphQL / OpenAPI / Metadata の探索  
10. Params 構造（ID/role/tenant/owner）解析  
11. Redirect / Error パターンの分析  

---

# 詳細手順

---

## 1. 公開URL・認証URLの探索
### 手順
1. 初回アクセスで redirect 先（/login /auth）を記録  
2. robots.txt / sitemap.xml を確認  
3. パスベースで以下を探索：
   - /auth /login /signup /reset  
   - /dashboard /user /admin /settings  

### 目的
- 認証境界の位置決定  
- “フロー入口” を整理して次の分析に繋げる  

---

## 2. JS ファイル一覧の収集
### 手順
1. DevTools → Network → JS で一覧取得  
2. ファイル名・chunk名・パス構造を記録  
3. map ファイルが存在する場合は必ず取得  

### 目的
- JS/API Mapping の準備  
- bundle 構造から機能単位を把握  

---

## 3. UI遷移 → XHR/fetch を全捕捉
### 手順
1. ログイン後の全メジャー画面を巡回  
2. Network タブで XHR/fetch をすべて記録  
3. 各 API の最初のレスポンス内容を即保存  

### 目的
- “実際に使われている API” を確実に把握  
- Flow / Wizard の入口も自動的に浮かび上がる  

---

## 4. API Path の分類（公開/認証/内部/管理）
### 手順
発見した API を以下のカテゴリに分類する：

#### 公開系
- /public/  
- /auth/  
- /status /health  

#### 認証系
- /api/v1/**  
- /user/{id}/  
- /settings/**  

#### 内部/隠し
- /internal/  
- /preview**  
- /system/**  
- /debug/**  
- /admin/**  

#### 管理系
- role/permission が必要なもの  
- 通常UIに存在しない操作（強制削除・plan変更等）

---

## 5. 機能軸での API グルーピング
以下の軸で API をグループ化すると BAC を当てやすくなる。

- user/account  
- organization/tenant  
- billing/plan  
- item/content/product  
- settings/profile  
- admin/permission  

### 目的
- “どの機能のどこに境界があるか” を視覚化できる  
- Logic/BAC 推論に直結する  

---

## 6. Hidden API / Admin API の探索
### 手順
1. JS 内で呼ばれていない API を一覧化  
2. /internal /admin /system /debug を直接探索  
3. next.js や nuxt の serverless function も探索（/api/**）

### 高確率でヒットするパターン
- UI にはない update/delete API  
- Admin 向けの “残骸 API”  
- Preview/Bulk import/export 用の API  

---

## 7. File Upload / Download / Export の特定
### Upload（要注意領域）
- /upload  
- /import  
- /media  
- /file/upload  
- Content-Type: multipart/form-data  

### Download / Export
- /download  
- /export  
- /report  
- /csv /excel  

### 目的
- 権限不備 or パストラバーサル の高リスク領域  
- 証跡としても重要  

---

## 8. Batch / list / search 系の抽出
### 手順
1. “list/search/all/batch” を含む API を抽出  
2. pagination / filter / sort の動作を見る  
3. レスポンス一覧に “他ユーザID” が混在しないか初期確認  

### 目的
- IDOR が最速で当たりやすい領域  
- multi-tenant の境界が晒される性質がある  

---

## 9. GraphQL / OpenAPI / Metadata の探索
### GraphQL
- /graphql  
- introspection が有効か確認（禁止の場合でも hint は得られる）  

### OpenAPI
- /swagger  
- /openapi.json  
- /api-docs  
- /v1/docs  

### Metadata
- /__meta  
- /config  

---

## 10. Params 構造（ID/role/tenant/owner）解析
特に BAC へ直結するので丁寧に記録する。

### 重点項目
- ID 系：userId, tenantId, orgId, accountId, resourceId  
- Role 系：role, scope, permission, plan  
- 所有権：ownerId, createdBy, updatedBy  

### 観点
- “自IDと他IDで違いが出る構造” を探す  
- これが BAC の入り口になる  

---

## 11. Redirect / Error パターンの分析
### 手順
1. login → dashboard → settings の通常遷移を記録  
2. 認証切れ時の挙動（401/302/redirect）を記録  

### 目的
- flow bypass の候補発見  
- backend がどの段階で認可しているか推測  

---

# 出力テンプレ（Kedappsec-notes へ貼る用）
~~~~
## Attack Surface Summary
- Public URLs:
- Auth URLs:
- JS Files:
- API Endpoints:
- Hidden/Internal APIs:
- Upload/Download/Export:
- List/Search/Batch:
- GraphQL/OpenAPI:
- Param Structure (ID/role/tenant/owner):
- Redirect/Error Patterns:
- Notes:
~~~~

---