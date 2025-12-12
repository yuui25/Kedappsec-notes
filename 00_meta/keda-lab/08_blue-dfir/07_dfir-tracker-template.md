<<<BEGIN>>>
# 08_blue-dfir / BASICS08-07 DFIRトラッカー（GAP/IMP/REQ/E-###の相互参照・状態遷移・KPI・報告会チェック）

## 1. 目的（本編STEPへの地続き）
本ファイルは、BASICS08-05/06 で作成した
- GAP-###（観測ギャップ）
- IMP-###（改善項目）
- REQ-###（REW/CC要求）
- E-###（Evidence）
を、実務で “回せる” 状態にする **進捗管理テンプレ** を固定する。

- DFIR章の unknown/no を **管理対象** にし、期限・担当・提出物を追跡する
- REQが満たされたら Evidence を更新し、Hop Findings を再生成して結論を更新する
- 報告会（Report-out）までに “何が確定し、何が未確定か” を明確化する
- 05_pentest-chain（STEP5）のDFIR章が、Evidence 기반で一貫するようにする

接続先：
- STEP5（05_pentest-chain）：DFIR章の確定度（yes/no/unknown）と Evidence 参照の整合
- BASICS08-01〜06：結論→ログ→Evidence→Findings→レポート→要求仕様
- 11_tools-lab：Run Log（実行記録）と相関キーの欠落検出
- 06_os-network-protocol：観測点ギャップの説明（なぜ unknown/no か）

---

## 2. 状態遷移（固定）
### 2-1. GAP ステータス
- open：未解消（unknown/no が残存）
- mitigated：改善/追加ログで “判断可能になった” が、完全解消ではない
- closed：observed_by_blue が yes または no（根拠固定）に確定し、報告書へ反映済

### 2-2. REQ ステータス
- proposed：提案済（まだ合意/承認前）
- accepted：合意済（期限・担当確定）
- in_progress：実施中
- blocked：障害あり（権限/契約/技術）
- done：提出物受領＆受入条件クリア
- rejected：却下（理由を固定し、DFIRはunknown/noのまま報告へ）

### 2-3. Evidence ステータス
- drafted：抽出・正規化中
- ready：raw/normalized/note が揃い、参照可能
- superseded：後続Evidenceに置換（上書き禁止のため、旧Evidenceは残す）

---

## 3. トラッカーの管理単位（固定）
本トラッカーは 4つの“台帳”で構成する。

1) Hop Findings Ledger（Hop別結論）
2) Gap Register（GAP-###）
3) Request Register（REQ-###）
4) Evidence Index（E-###）

各台帳は “相互参照キー” を必ず持つ（Hop↔GAP↔REQ↔E）。

---

## 4. Hop Findings Ledger（テンプレ）
Hop別の結論を “現時点の状態” として固定し、差分を追う。

~~~~
Hop Findings Ledger:
- hop: HopN
  hop_name: <...>
  event_scope: <...>
  observed_by_blue: yes|no|unknown
  confidence: high|medium|low
  correlation_tier: Tier-1|Tier-2|Tier-3|Tier-4
  evidence_basis: [E-###, ...] | []
  related_gaps: [GAP-###, ...] | []
  last_updated: YYYY-MM-DDTHH:MM+09:00
  next_action:
    - (REQ待ち/Evidence更新/再評価など)
~~~~

運用ルール（固定）：
- observed_by_blue が unknown/no の Hop は必ず related_gaps を持つ
- evidence_basis が空の Hop は原則許容しない（最低でも log_not_available の理由を固定）

---

## 5. Gap Register（GAP-### 台帳テンプレ）
BASICS08-05 の台帳を、進捗管理できる形に拡張する。

~~~~
Gap Register:
- gap_id: GAP-###
  hop: HopN
  event_scope: <...>
  observed_by_blue: unknown|no
  trigger: Trigger-A|B|C|D|E
  missing_component:
    - type: log_source|field|retention|access|integration|process|design
      detail: <...>
  consequence:
    - (どの主張/章/意思決定が弱くなるか)
  evidence_ref: [E-###, ...] | [log_not_available: ...]
  related_requests: [REQ-###, ...]
  related_improvements: [IMP-###, ...]
  status: open|mitigated|closed
  owner: client|both
  target_close_deadline: YYYY-MM-DD (or D1/D2/D3)
  notes:
    - (補足)
~~~~

運用ルール（固定）：
- GAP-### は必ず REQ-### に落とす（例外：rejected の場合は理由固定）
- closed になったら Hop Findings Ledger の observed_by_blue を更新し、レポートへ反映する

---

## 6. Request Register（REQ-### 台帳テンプレ）
BASICS08-06 の要求仕様を “進捗” と “受入” で管理する。

~~~~
Request Register:
- req_id: REQ-###
  type: REQ-LOG|REQ-RET|REQ-ACC|REQ-INT|REQ-COR|REQ-DET|REQ-OPS|REQ-ARC
  priority: P0|P1|P2
  related_gaps: [GAP-###, ...]
  related_hops: [HopN, ...]
  event_scope: <...>
  status: proposed|accepted|in_progress|blocked|done|rejected

  deadline:
    type: D1|D2|D3|D4
    detail: <...>

  owner_raci:
    R: <...>
    A: <...>
    C: <...>
    I: <...>

  deliverables:
    required:
      - raw
      - normalized
      - note
    optional:
      - settings_evidence
  acceptance_criteria:
    - ...
  received_artifacts:
    - evidence_ids_created: [E-###, ...]
    - received_date: YYYY-MM-DDTHH:MM+09:00
    - reviewer: client|tester
    - acceptance_result: pass|fail
    - fail_reason: (if fail)
  blockers:
    - (if blocked)
  comms_log:
    - date: YYYY-MM-DD
      with: <team/person>
      summary: <1-2 lines>
~~~~

運用ルール（固定）：
- done ＝ “受領” ではない。必ず acceptance_result=pass を満たす
- pass したら Evidence Index に E-### を登録し、Hop Findings を再評価する

---

## 7. Evidence Index（E-### 台帳テンプレ）
BASICS08-05 の Evidence Index を、状態と差分管理に対応させる。

~~~~
Evidence Index:
- evidence_id: E-###
  status: drafted|ready|superseded
  source_type: cloud-audit|auth-log|lm-log|edr|siem|http-edge|dns|other
  log_source_name: <...>
  hop_mapping: [HopN, ...]
  time_window_jst: <start> ~ <end>
  correlation_key:
    type: request_id|correlation_id|operation_id|session_id|logon_id|eventRecordId|none
  files:
    raw: <...>
    normalized: <...>
    note: <...>
  derived_from_req: REQ-### | none
  supersedes: E-### | none
  remarks:
    - (短い補足)
~~~~

運用ルール（固定）：
- REQ由来のEvidenceは derived_from_req を必ず持つ
- superseded は残す（参照断絶を防ぐ）

---

## 8. KPI（固定：unknown削減率・P0解消率）
DFIRを “改善プロジェクト” として動かすため、最低限のKPIを固定する。

### 8-1. KPI定義（固定）
- KPI-01：unknown_count（Hop別 + 重大イベント別の unknown 件数）
- KPI-02：unknown_reduction_rate（週次/報告会時点）
- KPI-03：P0_gap_close_rate（P0 の GAP closed 比率）
- KPI-04：REQ_on_time_rate（期限内に pass したREQ比率）
- KPI-05：Tier-1_coverage（Tier-1相関が成立したHop数）

### 8-2. KPIの計算ルール（簡易）
- unknown_reduction_rate = (initial_unknown - current_unknown) / initial_unknown
- P0_gap_close_rate = closed_P0_gaps / total_P0_gaps
- REQ_on_time_rate = ontime_pass_REQs / total_pass_REQs

---

## 9. 報告会（Report-out）までのチェックリスト（固定）
### 9-1. D-7（7日前）までに必須
- P0 REQ が accepted されている
- 主要な Evidence（Hop2/5/7）の ready 化が進んでいる
- unknown/no の GAP が台帳化されている（抜けなし）

### 9-2. D-2（2営業日前）までに必須
- Hop1〜7 の Hop Findings が “最終版” で再生成済
- DFIR章の Evidence参照が全て解決（E-###が引ける）
- rejected/blocked の REQ は理由が固定され、unknown/no の説明が準備できている

### 9-3. 当日（Report-out）
- Executive DFIR Summary の数値（unknown件数、P0解消率）が最新
- 改善ロードマップ（IMP-###）が GAP/REQ とリンクし、顧客アクションが明確
- “何が分かり、何が分からないか” が yes/no/unknown で一意に示されている

---

## 10. 付録：トラッカー運用の最小ルール（固定）
- 更新単位は “Evidence ready” と “REQ pass”
- 会議の議事録（comms_log）は最低限残す（後追い可能にする）
- トラッカーは “報告書の整合” を守るための装置であり、口頭合意を放置しない

---

## 11. 次のファイル（BASICS08-08）への接続
次は、DFIRトラッカーを前提に、Blue/Red/Client の役割分担と情報授受の “現場運用手順” を固定する。
（誰が・いつ・何を渡し、どこで合意し、どの形でEvidence化するか）

- 次：keda-lab/08_blue-dfir/08_dfir-operating-model.md
  - 役割（Blue/Red/Client/Third-party）とRACI拡張
  - ログ抽出会（Evidence Workshop）の進め方
  - 例外処理（権限不可・保持切れ・秘密情報）
  - 監査・法務・個人情報（PII）取り扱い最小ルール
<<<END>>>
