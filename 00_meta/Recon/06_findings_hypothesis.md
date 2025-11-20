# 06_Findings_Hypothesis (L3 Detailed Manual)

本ドキュメントは、Tech Fingerprinting → Attack Surface → JS/API Mapping →  
Cloud Misconfig → BAC/Logic Candidates の各フェーズで収集した情報を、  
**脆弱性候補（Findings）と仮説（Hypothesis）として体系化する手順書** である。

Recon の結果は “情報の山” になるため、それを  
**権限不備・ロジックフロー系脆弱性の仮説へ変換する役割** を持つ。

---

# 目的
- Recon で得た情報を「脆弱性の仮説」へ変換  
- 当たりやすい優先候補を特定  
- UI / API / Cloud の不整合を抽出  
- ID / tenant / role / owner の境界破りを整理  
- Flow / Wizard / State の破綻点を仮説化  
- 後続の “Next Action（深掘りテスト）” に渡す土台を作る  

---

# 構成（Overview）
1. Core Identity / Boundary Hypothesis  
2. API Capability Hypothesis  
3. UI vs API Gap Hypothesis  
4. IDOR Hypothesis  
5. Role Escalation Hypothesis  
6. Tenant/Org Break Hypothesis  
7. Ownership Break (ownerId) Hypothesis  
8. Flow/Wizard Bypass Hypothesis  
9. Hidden API Access Hypothesis  
10. State Machine Hypothesis  
11. Cloud Misconfig → BAC 合流 Hypothesis  
12. 最終候補（Top Findings Hypothesis）  

---

# 詳細手順

---

## 1. Core Identity / Boundary Hypothesis

### 手順
1. userId / accountId / orgId / tenantId / ownerId を一覧化  
2. それぞれが “どの境界” を表しているか整理  
3. 下記の仮説例を立てる：

### 仮説例
- “tenantId は組織全体の境界であり、任意変更で越境する可能性”  
- “ownerId は実リソース所有権であり、UI では固定だが API では変更可能”  
- “userId と accountId の境界が曖昧で、他者のデータにアクセスできる可能性”  

---

## 2. API Capability Hypothesis

API Capability Matrix（CRUD分類） を元に仮説を作成。

### 仮説例
- “API に update/delete が存在するが UI には存在しない → 権限不備の可能性”  
- “list API が広い権限を持っている → 他者データ混在の可能性”  
- “resource 作成時に ownerId が固定されていない → Ownership bypass 可能”  

---

## 3. UI vs API Gap Hypothesis（重要）

UI と API の差分は最も高確度の BAC 候補。

### 仮説例
- “UI では role 変更不可なのに API は受け付けている”  
- “UI は tenant 固定だが API は可変 → 越境可能”  
- “UI には Delete ボタンなし → API に delete が存在 → 不備候補”  

---

## 4. IDOR Hypothesis

IDOR（他者IDアクセス）の全候補をここで確定。

### 仮説例
- “GET /item/{id} の {id} が制御されていない可能性”  
- “update API が itemId を自由に変更できる可能性”  
- “download API が fileId の境界チェックをしていない可能性”  

---

## 5. Role Escalation Hypothesis

### 条件例
- API に role, permission, scope, isAdmin が存在  
- UI に role 設定画面なし  
- hidden admin API が存在  

### 仮説例
- “role=admin を直接指定すれば昇格する可能性”  
- “ユーザ登録時に role フィールドが無制限に入力可能”  
- “developer/admin 残骸 API にアクセスできる可能性”  

---

## 6. Tenant/Org Break Hypothesis（高難度・高報奨金）

multi-tenant SaaS で最重要領域。

### 仮説例
- “tenantId を任意に変更できる可能性がある”  
- “list API の結果に複数 tenant のデータが混ざる”  
- “tenantId がレスポンスに含まれ、再利用できる”  
- “tenantId を削除すると別テナントに落ちる可能性”  

---

## 7. Ownership Break Hypothesis（ownerId）

### 仮説例
- “update/create API で ownerId を自由に指定できる”  
- “ownerId がレスポンスに含まれ、そのまま利用可能”  
- “ownerId が固定されていないため、他者のリソースを奪える可能性”  

---

## 8. Flow/Wizard Bypass Hypothesis

ステップフロー・確認画面・完了画面などの突破可能性。

### 仮説例
- “/confirm を直接叩くとフローをショートカットできる”  
- “hidden パラメータが backend 未検証の可能性”  
- “セッション破壊後でも完了画面が開く可能性”  
- “ログインし直しても旧データが保持される構造 → flow bypass の兆候”  

---

## 9. Hidden API Access Hypothesis

JS 内に存在するが UI 非表示の API を元に仮説化。

### 仮説例
- “/internal/list が認可なしで参照できる可能性”  
- “/admin/** の残骸 API が role-check されていない可能性”  
- “preview/bulk API が本番環境で有効”  

---

## 10. State Machine Hypothesis

リソース状態（draft/published/archived など）の遷移の不整合。

### 仮説例
- “draft → published の遷移が UI では不可だが API では可能”  
- “archived 状態から update が通る可能性”  
- “承認フローに server-side check がなく bypass 可能”  

---

## 11. Cloud Misconfig → BAC 合流 Hypothesis

Cloud/SaaS の設定不備が権限不備に直結するケース。

### 仮説例
- “preview backend（staging）が public → admin API にアクセス可能”  
- “公開された S3 から role/config が取得できる可能性”  
- “API Gateway の CORS が緩い → unauthorized でも叩ける可能性”  

---

## 12. 最終候補（Top Findings Hypothesis）

上記 1〜11 の仮説の中から、  
**最も当たりやすい候補を 3〜7 個に絞り、優先順位付きで記述する。**

### 例
1. tenantId が可変で境界を越える可能性  
2. ownerId が update API で自由入力可能  
3. UI にない delete API が存在する  
4. /admin/list が role-check されていない可能性  
5. flow bypass により confirm を直接叩ける可能性  

---

# 出力テンプレ（Kedappsec-notes 用）
~~~~
## Findings & Hypothesis Summary

### 1. Core Boundary Hypothesis
- userId:
- tenantId/orgId:
- ownerId:
- resourceId:
- 仮説:

### 2. API Capability Hypothesis
- CRUD gaps:
- update/delete anomalies:
- 仮説:

### 3. UI vs API Gap Hypothesis
- Missing UI features:
- Hidden API:
- tenant/role inconsistencies:
- 仮説:

### 4. IDOR Hypothesis
- list/detail/update candidates:
- file/download candidates:
- 仮説:

### 5. Role Escalation Hypothesis
- role fields:
- admin API traces:
- 仮説:

### 6. Tenant/Org Break Hypothesis
- tenantId behavior:
- cross-tenant indications:
- 仮説:

### 7. Ownership Hypothesis
- ownerId behavior:
- createdBy behavior:
- 仮説:

### 8. Flow/Wizard Hypothesis
- step URLs:
- confirm/finish bypass:
- session behavior:
- 仮説:

### 9. Hidden API Hypothesis
- internal/admin/debug:
- preview/bulk:
- 仮説:

### 10. State Machine Hypothesis
- transitions:
- forbidden transitions:
- 仮説:

### 11. Cloud → BAC Hypothesis
- dev/staging exposure:
- public bucket artifacts:
- CORS looseness:
- 仮説:

### 12. Top Findings Hypothesis (Priority)
1.
2.
3.
4.
5.
6.

~~~~

---