# Recon (L3) – Web / App / API  
本ドキュメントは、Web/App/API を対象とする L3 ブラックボックス診断のための  
**再現性のある Recon ワークフロー・テンプレート** である。

---

# 0. Project Form（自動生成用テンプレ：C）
以下は、診断開始時に ChatGPT に投げるための **フォーム式テンプレ**。

~~~~
# Recon Input Form
- Target Name:  
- Base URL(s):  
- Auth Method (Session/Cookie/JWT/OAuth/OIDC):  
- Roles/Plans Known (if any):  
- Notes (認証方式・前提条件など):

# Expected Output
上記の Target 情報をもとに以下の構造で Recon Overview (L3) を生成すること：
1. Tech Fingerprinting  
2. Attack Surface Mapping  
3. JS / API Mapping  
4. SaaS/Cloud Misconfig（対象外ならN/A）  
5. BAC / Logic Hypothesis  
6. Next Actions  
~~~~

---

# 1. Tech Fingerprinting (L3)
対象の技術スタック・境界・依存関係を特定し、  
後続の BAC / Logic Flaw 推論に利用する。

- Hosting / CDN / WAF  
- Server stack (nginx, IIS, Apache, Node.js など)  
- Framework / Runtime（Rails / Laravel / Spring / Next.js / Nuxt / React / Vue）  
- SPA / SSR 判定  
- Token Storage（Cookie / localStorage / sessionStorage）  
- Session Lifecycle（rotation / expiration / SSO）  
- OAuth/OIDC hints（/authorize, /.well-known/, scope）  
- CSP / CORS 分析（外部依存を抽出）  
- Client build artifact（/static/ /assets/ mapファイル）  
- Feature Flag / Config JS（env.js, settings.js 等）  

---

# 2. Attack Surface Mapping (L3)
全ての「入口・境界・API」を体系的に列挙する。

- Public URL / Auth-required URL  
- Onboarding / Wizard / Step-based flows  
- JSファイル一覧（生成元、ビルド方式）  
- fetch/XHR パターンの抽出  
- API Path（/api/, /v1/, /internal/）  
- Hidden API / Admin branch の存在  
- ファイルアップロード系（/upload /import）  
- Export/Download（/export, /download）  
- 検索・一覧（/list, /search）  
- Batch API（複数リソースを並列取得）  
- OpenAPI / graphql / meta endpoints  
- Params 調査
  - ID系（userId / orgId / tenantId / accountId / itemId）  
  - Role系（role, permission, scope）  
  - Owner系（ownerId, createdBy, updatedBy）  

---

# 3. JS / API Mapping (L3)
JS を起点にバックエンド権限モデルを逆算する Blackbox 推論フェーズ。

## 3.1 fetch/XHR 一覧
- Method / Path  
- Headers（auth, custom-token）  
- Request Body  
- 返却 JSON（ID/role/owner/tenant）  

## 3.2 APIの意味づけ（権限区分）
- 読み取り系（list/detail）  
- 更新系（update/patch）  
- 作成系（create/register）  
- 削除系（delete/remove）  
- 代表権限（admin-only?）  
- 組織境界（tenantId / orgId の扱い）  

## 3.3 Hidden endpoint
- UIから呼ばれない API  
- JS で条件分岐される API  
- Admin 機能の名残（/admin/, /internal/）  
- Preview / Draft 系 API  

---

# 4. SaaS / Cloud Misconfig（対象外なら N/A）
Cloud/SaaS 依存性による攻撃面を推定。

- CDN (Cloudflare/Fastly/Akamai) 誤設定  
- GitHub Pages / Netlify / Vercel / S3 hosting  
- Firebase / Supabase  
- Public bucket / public read  
- API Gateway / Lambda permission  
- CORS misconfig  
- 本番・検証環境混在  

---

# 5. BAC / Logic Flaw Candidates（L3核心）
Blackboxでも権限不備を高速に当てるための推論テンプレ。

## 5.1 IDOR（他IDアクセス）
- GET: /item/{id}, /user/{id}, /org/{id}  
- POST/PUT: payload の id 置き換え  
- リスト系で「他者データ混入」が発生するか  

## 5.2 Role/Permission 不備（UIとAPIの差）
- role, isAdmin, permission, scope  
- UIで変更不能な属性が API に存在しないか  
- role による機能制限が API にない  

## 5.3 所有権（Owner/tenant）境界
- tenantId / orgId / ownerId の書換  
- Multi-tenant での境界維持確認  
- 親子関係（account → user → item） の破綻点  

## 5.4 Flow bypass（wizard 系）
- /step-1 → /step-3 の直打ち  
- セッション破壊後の完了画面アクセス  
- 編集フロー中に値を書換  

## 5.5 Hidden API 利用
- UIにない update/delete  
- Preview/Batch の利用で他ユーザのリソース操作  

---

# 6. Findings / Hypothesis（仮説整理）
Reconの結果から、当たり候補を仮説化。

- ID境界の弱い領域  
- role の取り扱いが甘いAPI  
- tenantId の固定／外部可変  
- UI と API の不整合  
- Flow制御に依存している領域  

---

# 7. Next Actions（深掘りステップ）
- ID切替テスト  
- role/tenant 書換テスト  
- Flow bypass テスト  
- Hidden API direct access  
- Session lifecycle manipulation  
- Token refresh / rotation テスト  

---

# 付録：L3 BAC First-Hit Checklist（30分以内）
最も成果が出やすい優先領域。

1. ID系（userId / tenantId / itemId）を抽出  
2. 一覧API（/list, /search）の応答確認  
3. Hidden API direct access  
4. Flow（step-based）の突破  
5. UIに存在しない role/permission フィールドの探索  

---