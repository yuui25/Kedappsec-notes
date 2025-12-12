<<<BEGIN>>>
# 08_blue-dfir / BASICS08-06 REW/CC要求仕様（REQ採番・要求分類・提出物形式・期限/優先度・RACI固定）

## 1. 目的（本編STEPへの地続き）
本ファイルは、BASICS08-05（DFIRレポート組み立て）で作った
- GAP-###（Observability Gap Register）
- IMP-###（Improvement Roadmap）
を、STEP5-14（REW/CC：Request Evidence / Client Cooperation）として **顧客に提示できる要求仕様** に変換する。

- unknown/no を “改善提案” で終わらせず、**要求（REQ-###）として契約・運用に落とす**
- 要求の対象（ログ/保持/権限/連携/検知/運用）を分類し、責任分界（RACI）を固定
- 提出物を Evidence Pack 互換に統一し、DFIR章へ **機械的に取り込める** 状態にする
- 期限（保持期限/報告会/監査要件）と優先度（P0〜P2）で実行性を担保する

接続先：
- STEP5（05_pentest-chain）/ STEP5-14（REW/CC）：顧客要求の正式化
- BASICS08-01〜05：unknown/no → GAP → IMP → REQ の地続き
- 11_tools-lab：Run Log / Evidence Pack の提出要件
- 06_os-network-protocol：観測点・相関キー・保持設計の裏付け

---

## 2. REQ（要求）モデル（固定）
REQ は「何を」「誰が」「いつまでに」「どの形式で」「なぜ必要か」を固定する管理単位。

### 2-1. REQ採番ルール（固定）
- REQ-### は案件単位の通し番号（REQ-001〜）
- GAP-### 由来の要求は必ず `related_gaps` を持つ
- IMP-### 由来の要求は必ず `related_improvements` を持つ
- 1つの GAP から複数 REQ が生じることを許容（例：権限付与REQとログ抽出REQ）

禁止：
- REQ の上書き再利用（差分は新規REQ）
- REQ が Evidence Pack 形式と矛盾する提出（形式ブレ禁止）

---

## 3. 要求分類（固定：REQタイプ）
REQ は必ず type を持ち、typeごとに提出物と責任分界が異なる。

### 3-1. REQ type 一覧（固定）
- REQ-LOG：ログ抽出/提供（raw/normalized/note）
- REQ-RET：保持（retention延長、アーカイブ、エクスポート設計）
- REQ-ACC：権限/アクセス（抽出権限、閲覧権限、API権限）
- REQ-INT：連携（SIEM/Log Lake転送、コネクタ、スキーマ維持）
- REQ-COR：相関（correlation_id採番、trace引き回し、キー保持）
- REQ-DET：検知（アラート条件、ユースケース、ルールチューニング）
- REQ-OPS：運用（抽出手順、担当者、手順書、インシデント対応フロー）
- REQ-ARC：アーキ/設定証跡（設定画面、設計書、仕様根拠：no確定用）

注：
- “unknownをyesにしたい” は REQ-LOG/ACC/RET/COR/INT の組合せで実現する
- “noを根拠化したい” は REQ-ARC（設計根拠）で固める

---

## 4. 優先度（固定：P0〜P2）と期限（固定）
### 4-1. 優先度の固定ルール（再掲＋REQ向け具体化）
- P0：Hop2/5/7 に直結する unknown/no を解消（認証・権限・監査停止）
- P1：Hop4（横展開）を追えるようにする（EDR/host監査）
- P2：Hop1（入口）と Hop6（目的達成）の範囲確定（edge/データ操作）

### 4-2. 期限（deadline）設定の固定ルール
- D1：保持期限に間に合う最短（例：7日以内、ログ消失回避）
- D2：報告会まで（例：報告会日 - 2営業日）
- D3：改善ロードマップに合わせる（30/90/180日）
- D4：監査/規制/契約要件に合わせる（必要なら）

deadline は必ず次のどれかで宣言する：
- `deadline_type: D1|D2|D3|D4`
- `deadline_detail: <具体日付 or 相対>`

---

## 5. 提出物形式（固定：Evidence Pack互換）
REQ の提出物は BASICS08-03 の Evidence Pack に揃える。

### 5-1. 提出物の固定セット
- raw（必須）
- normalized（必須）
- note（必須）
- (任意) settings_evidence（REQ-ARC の場合：スクショ/設定出力/設計書抜粋）

### 5-2. フィールド要件（最低限）
normalized は common schema を満たす：
- evidence_id（E-###）※顧客提供物でも E-### を付与してよい（提供Evidenceとして採番）
- timestamp_iso_jst
- actor / action / outcome
- source / target
- correlation_key_type/value（可能な範囲で）

禁止：
- スクショだけの提出（logの再現性がない）
- PDF貼り付けだけ（正規化不可）
（例外：REQ-ARC の “設計根拠” のみスクショ/設計書が主となり得る）

---

## 6. 責任分界（RACI）固定
REQ の実行性を担保するため、RACI を必ず付ける。

### 6-1. RACI 定義（固定）
- R (Responsible)：作業実行者（抽出/設定/連携）
- A (Accountable)：最終責任者（承認・提供の決裁）
- C (Consulted)：相談先（設計・運用・法務）
- I (Informed)：報告先（関係者）

### 6-2. 典型RACI（例：REQ-LOG）
- R：顧客（ログ管理者/運用）
- A：顧客（システムオーナー）
- C：テスター（抽出条件の提示）
- I：顧客CSIRT/監査

案件により “both” を許容するが、R/A は必ず顧客側に置く（現実的運用のため）。

---

## 7. REQ テンプレ（コピペ用：固定）
### 7-1. 最小版（必須）
~~~~
REQ-###:
- type: REQ-LOG|REQ-RET|REQ-ACC|REQ-INT|REQ-COR|REQ-DET|REQ-OPS|REQ-ARC
- priority: P0|P1|P2
- related_gaps: [GAP-###, ...] | []
- related_improvements: [IMP-###, ...] | []
- related_hops: [HopN, ...]
- event_scope: <signin/role_assign/api_access/...>

- request:
  - what: (何を提供/設定/連携/実装するか)
  - why: (observed_by_blue を確定/unknown解消/相関強化 等)
  - minimum_requirements:
    - deliverables: raw + normalized + note | settings_evidence
    - time_window_jst: <start> ~ <end>
    - fields: (common schemaの必須項目 + 追加)
    - correlation_key: (優先順位つき)
  - format: csv|json|evtx|portal_export|api_export|kql_result|other

- deadline:
  - deadline_type: D1|D2|D3|D4
  - deadline_detail: <...>

- rici:
  - R: client|tester|both (with role)
  - A: client (role)
  - C: client|tester (role)
  - I: <stakeholders>

- acceptance_criteria:
  - (完了条件：例 correlation_id が含まれる / 保持がX日 / SIEMに転送される)

- evidence_linkage:
  - will_create_evidence: yes|no
  - evidence_ids: [E-###,...] (if already known)
~~~~

### 7-2. 詳細版（推奨：契約/要件提示向け）
~~~~
REQ-###:

meta:
  type: REQ-LOG|REQ-RET|REQ-ACC|REQ-INT|REQ-COR|REQ-DET|REQ-OPS|REQ-ARC
  priority: P0|P1|P2
  status: proposed|accepted|in_progress|done|blocked
  owner_team: <client team name>
  related:
    gaps: [GAP-###, ...]
    improvements: [IMP-###, ...]
    hops: [HopN, ...]
    evidence: [E-###, ...]
  rationale_1line: (unknown解消 / no根拠化 / yes強化)

request_spec:
  scope:
    event_scope: <...>
    time_window_jst:
      start: YYYY-MM-DDTHH:MM:SS+09:00
      end:   YYYY-MM-DDTHH:MM:SS+09:00
    systems:
      - <system/app/tenant/host>
  deliverables:
    - name: raw
      required: true
      format: csv|json|evtx|...
      notes: (抽出元・改変禁止)
    - name: normalized
      required: true
      format: csv
      schema: common_schema_v1
    - name: note
      required: true
      format: md
      template: evidence_note_v1
    - name: settings_evidence
      required: false
      notes: (REQ-ARC等)
  minimum_fields:
    required:
      - timestamp_iso_jst
      - actor
      - action
      - outcome
      - source
      - target
    correlation:
      preferred:
        - correlation_id
        - request_id
        - operation_id
        - session_id
      fallback:
        - eventRecordId
        - time+actor+target
  extraction_method:
    route: A Portal | B API | C SIEM
    query_or_steps: (再現可能な手順、またはクエリ)
  security_constraints:
    redaction_policy: (PIIの取り扱い、マスキング方針)
    transfer_method: (安全な転送経路)
  deadline:
    type: D1|D2|D3|D4
    detail: <...>
  RACI:
    R: <...>
    A: <...>
    C: <...>
    I: <...>
  acceptance_criteria:
    - (例) normalized に correlation_id が100%含まれる
    - (例) 保持が90日に延長されたことを設定証跡で確認
  risks_if_not_met:
    - (例) Hop5がunknownのままとなり、権限昇格の追跡性を評価できない
~~~~

---

## 8. REQ → DFIR章への取り込み（固定）
REQ が満たされたら、Evidence Pack を更新し、Hop Findings を再生成する。

### 8-1. 更新ルール
- REQ対応で新しいログが出たら新規 Evidence（E-###）を採番
- Hop Findings の evidence_basis に E-### を追加し、observed_by_blue を再評価
- GAP-### の状態を更新（open → mitigated/closed）

---

## 9. 典型REQパターン（すぐ使える雛形）
### 9-1. Hop2（認証）P0：サインインログ抽出（REQ-LOG）
~~~~
REQ-001:
- type: REQ-LOG
- priority: P0
- related_hops: [Hop2]
- event_scope: signin
- request:
  - what: IdP sign-in logs export including correlation_id and MFA detail
  - why: Determine observed_by_blue=yes/unknown for authentication hop with Tier-1 correlation
  - minimum_requirements:
    - deliverables: raw + normalized + note
    - time_window_jst: <start> ~ <end>
    - fields: timestamp, actor, src_ip, app, outcome, correlation_id, mfa_detail
    - correlation_key: correlation_id (preferred)
  - format: csv|json
- deadline:
  - deadline_type: D1
  - deadline_detail: within 7 days (retention risk)
- rici:
  - R: client (IdP admin)
  - A: client (system owner)
  - C: tester (filter guidance)
  - I: client CSIRT
- acceptance_criteria:
  - correlation_id present for all matching sign-ins
~~~~

### 9-2. Hop5（権限）P0：監査有効化（REQ-RET/ARC/INT）
- REQ-RET：監査ログ保持延長
- REQ-ARC：監査有効化状態の設定証跡
- REQ-INT：SIEM転送（operation_id保持）

---

## 10. 次のファイル（BASICS08-07）への接続
次は、REQ（要求）と Evidence（E-###）と Hop Findings を “進捗管理” できる状態にする。
つまり、DFIRを **プロジェクトとして回せる管理テンプレ** を作る。

- 次：keda-lab/08_blue-dfir/07_dfir-tracker-template.md
  - GAP/IMP/REQ/Evidence の相互参照テーブル
  - 状態遷移（proposed→accepted→done）
  - 報告会までのチェックリスト
  - “unknown削減率” などのKPI
<<<END>>>
