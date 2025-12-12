<<<BEGIN>>>
# 11_tools-lab / BASICS11-02 実行ログテンプレ（Evidence Index / Timeline へ直結）

## 1. このテンプレが補う本編STEP（01〜05への地続き）
- STEP1（Recon）：入口・到達可能性の“観測”を Evidence 化して残す
- STEP2（Auth/SSO/MFA）：サインイン/CA/MFA/到達範囲を L1/L2 で記録する
- STEP3（AD/横展開）：列挙→宛先選定→横展開の根拠（ログ/相関キー）を残す
- STEP4（Cloud）：監査ログ＋API最小サンプルを L1/L2 で残す
- STEP5（運用/提出）：Evidence Index / Timeline / Report を“データから埋める”前提を作る（STEP5-09〜15）

---

## 2. 運用ルール（固定：この順に記録する）
1) **実行前**：狙う Claim レベル（L1/L2/L3）と「証明したいこと」を決める  
2) **実行直後**：実行ログ（Run Log）を埋める（まだ Evidence ID は未採番で可）  
3) **保存**：raw/sanitized を保存し、Evidence ID を採番する  
4) **台帳化**：Evidence Index（STEP5-09）へ 1 Evidence = 1 行で登録する  
5) **時系列化**：Timeline（STEP5-09）へイベントとして登録する  
6) **リンク**：Run Log の `evidence_ids` を更新し、Report（STEP5-10）に貼れる状態にする  

---

## 3. Run Log（実行ログ）テンプレ：コピペ用（最小→詳細）

### 3-1. Run Log（最小版：必須）
以下が埋まっていれば、Evidence Index / Timeline に落とせる。

~~~~
run_id: RUN-YYYYMMDD-###
hop: HopN
scenario: A|B|C|D|E|NA
purpose: (この実行で証明したいことを1文)
claim_target: L1|L2|L3
actor: (user:xxx / role:xxx / tester:xxx)
target: (host/url/resource)
time_start_jst: YYYY-MM-DDTHH:MM:SS+09:00
time_end_jst: YYYY-MM-DDTHH:MM:SS+09:00
tool: (name + version)
action_summary: (機微を避けた要約)
output_1line: (結果を1行で)
correlation_key: (session_id/request_id/eventRecordId 等、あれば)
evidence_ids: (E-###;E-### / まだなら TBD)
safety_notes: (データ最小化/非実行理由/ROE制約)
~~~~

### 3-2. Run Log（詳細版：推奨）
最終的に“監査に耐える”のは詳細版。

~~~~
run_id: RUN-YYYYMMDD-###
hop: HopN
scenario: A|B|C|D|E|NA

objective:
  purpose_1line: (証明したいこと)
  success_criteria: (成功の定義：何が出ればOKか)
  claim_target: L1|L2|L3
  roe_constraints: (禁止事項/非実行範囲)

context:
  actor: (user: / role: / tester:)
  actor_auth_context: (MFA有無、セッション種別、端末条件など観測できる範囲)
  target:
    type: host|url|resource|tenant|saas
    name: (例：dc01 / https://sso.example / EntraID Tenant 等)
    environment: onprem|cloud|hybrid|saas
  preconditions: (前提：Hop1/2/3の成果物、到達条件)

execution:
  time_start_jst: YYYY-MM-DDTHH:MM:SS+09:00
  time_end_jst: YYYY-MM-DDTHH:MM:SS+09:00
  tool:
    name: 
    version:
    config_notes: (重要設定：プロキシ/認証/スロットリング等)
  action_detail_sanitized: (本文に出して良い範囲で手順を要約)
  command_or_request_raw_ref: (raw側に保存したファイル名/パス参照)
  output_raw_ref: (raw側の出力ファイル参照)

result:
  output_1line: (結果)
  key_observations:
    - (観測点1)
    - (観測点2)
  claim_achieved: L1|L2|L3|failed
  why: (成功/失敗の理由を1〜2文)
  next_step_decision: (次Hopへ繋ぐ判断：宛先選定理由)

evidence:
  evidence_ids: (E-###;E-### または TBD)
  artifacts:
    - artifact_type: log|command_output|screenshot|file|note
      raw_path: evidence/.../raw/...
      sanitized_path: evidence/.../sanitized/...|-
      sha256_raw: sha256:...
      sha256_sanitized: sha256:...|-
      confidentiality: public|internal|confidential|restricted
      pii_flag: yes|no
      secrets_flag: yes|no
  correlation_key:
    type: session|request|eventRecord|other
    value: (相関キー)
  timeline_event_ids: (T-####;T-#### / まだなら TBD)

quality_gate:
  metadata_complete: yes|no
  sanitized_ready_for_report: yes|no
  needs_followup: (何が足りないか)
~~~~

---

## 4. Evidence（raw/sanitized）保存テンプレ（ファイル命名まで固定）

### 4-1. 命名規則（推奨：追跡可能にする）
- Evidence ID を採番後、ファイル名先頭に付与する  
  - 例：`E-070_graph-api-sample.json`
  - 例：`E-060_entra-audit-export.csv`
- 同一 Evidence の派生（raw/sanitized）は同じ ID で揃える  
  - raw：`E-070_*.json`
  - sanitized：`E-070_*.md`（本文引用に強い）

### 4-2. 保存場所（STEP5-07 構造に従う）
- `evidence/hopN_xxx/raw/`
- `evidence/hopN_xxx/sanitized/`

### 4-3. raw と sanitized の分離基準（最低ライン）
- raw：トークン/クッキー/秘密鍵/個人情報/内部ホスト名/IP/ユーザー名が含まれ得る
- sanitized：上記をマスキングし、**成立の根拠（Fact）だけ残す**

---

## 5. Sanitized（マスキング）最小指針（Reportに貼れる形）

### 5-1. マスキング対象（原則）
- 個人：氏名、メール、社員番号、電話、住所（識別可能なもの）
- 認証：パスワード、秘密鍵、セッションID、Bearer、Refresh Token、Cookie
- 内部：内部IP、内部ホスト名、共有パス、テナント固有ID（必要最小に短縮/置換）

### 5-2. 置換ルール（表現統一）
- ユーザー：`user:alice` → `user:<USER_A>`
- メール：`alice@example.com` → `<USER_A>@<DOMAIN>`
- ホスト：`dc01.corp.local` → `<HOST_DC01>`
- IP：`10.0.1.10` → `<IP_INT_01>`
- トークン：`eyJ...` → `<TOKEN_REDACTED>`
- Tenant/Subscription：`xxxxxxxx-xxxx...` → `<TENANT_ID_REDACTED>`

### 5-3. sanitized の書式（推奨：短い“Factブロック”）
~~~~
Evidence: E-070
When: 2025-11-20T10:05:00+09:00
Where: Graph API
Actor: role:<ROLE_A>
Claim: L2 (Demonstrated)

Fact:
- API へ認可済みコンテキストでアクセスが成功した（HTTP 200）
- 取得したのはメタ情報のみ（件名/ID/更新者等）で、本文は取得していない（ROE準拠）

Reference:
- raw: evidence/hop7_cloud_api/raw/E-070_graph-api-sample.json (restricted)
- sha256(raw): sha256:...
~~~~

---

## 6. Run Log → Evidence Index（STEP5-09）への転記ルール

### 6-1. 転記対応（必須カラム）
- `evidence_id`：E-###
- `title`：Run Log の `output_1line` を短縮して名詞句化
- `hop`：Run Log の `hop`
- `claim_level`：Run Log の `claim_achieved`（なければ `claim_target`）
- `collected_at_iso_jst`：Run Log の `time_end_jst`（原則）
- `source_type`：Run Log の `target.environment` を基準に `onprem/cloud/saas/osint`
- `source_name`：Run Log の `target.name`
- `actor`：Run Log の `actor`
- `artifact_type`：保存したファイル種別
- `path_raw/path_sanitized`：保存パス
- `sha256_*`：ハッシュ
- `confidentiality/pii_flag/secrets_flag`：Run Log の evidence 記載

### 6-2. 1 Evidence = 1 行の崩し方（例外ルール）
- 例外：1 回の実行で複数種の証拠が出る場合  
  - 監査ログ（E-060）＋ APIサンプル（E-070）など  
  → Evidence を分割し、Run Log に `evidence_ids` を複数紐付ける（推奨）

---

## 7. Run Log → Timeline（STEP5-09）への転記ルール

### 7-1. 最小転記（必須）
- `event_id`：T-####
- `time_iso_jst`：Run Log の `time_end_jst`
- `scenario`：Run Log の `scenario`
- `hop`：Run Log の `hop`
- `event_type`：Hopに応じて固定（例：signin/discovery/lateral/api_access）
- `actor`：Run Log の `actor`
- `target`：Run Log の `target.name`
- `result`：success/fail/observed
- `claim_level`：Run Log の `claim_achieved`
- `evidence_ids`：Run Log の `evidence_ids`
- `narrative_1line`：STEP5-09 テンプレで生成（Hop叙述に貼る）

---

## 8. 具体例（短いサンプル：Hop7 / API-SAMPLE）

### 8-1. Run Log（最小版）
~~~~
run_id: RUN-20251213-001
hop: Hop7
scenario: A
purpose: Graph API により対象リソースへアクセス可能性を最小限で実証する
claim_target: L2
actor: role:<ROLE_A>
target: resource:GraphAPI:<RESOURCE_X>
time_start_jst: 2025-12-13T09:12:10+09:00
time_end_jst: 2025-12-13T09:13:02+09:00
tool: script:graph-client v0.1
action_summary: メタ情報のみ取得するAPI呼び出しを実施
output_1line: HTTP 200 によりアクセス可能性を実証（本文取得なし）
correlation_key: request_id:<REQ_01>
evidence_ids: E-070
safety_notes: ROEにより本文取得は非実行、メタ情報のみサンプリング
~~~~

### 8-2. Evidence Index 行（要点）
- `evidence_id`：E-070
- `hop`：Hop7
- `claim_level`：L2
- `artifact_type`：command_output
- `path_raw`：evidence/hop7_cloud_api/raw/E-070_graph-api-sample.json
- `path_sanitized`：evidence/hop7_cloud_api/sanitized/E-070_graph-api-sample.md

### 8-3. Timeline 行（要点）
- `event_type`：api_access
- `result`：success
- `claim_level`：L2
- `evidence_ids`：E-070
- `narrative_1line`：API 経由で対象リソースへのアクセス可能性を最小限のサンプリングで実証した（E-070）

---

## 9. 次のファイル（BASICS11-03）への接続
次は「ツール→Evidence→Hop→章」へ落とし込む対応表を作り、運用を機械化する。

- 次：keda-lab/11_tools-lab/03_tool-to-evidence-mapping.md
  - Hop別の必須 Evidence（STEP5-12 の種類）を、具体ツール出力に割り当て
  - “このツールを回せばこの章が埋まる”を明示
  - 過収集を防ぐための最小出力（サンプリング粒度）も固定

<<<END>>>
