<<<BEGIN>>>
# 08_blue-dfir / BASICS08-01 DFIR結論テンプレ（yes/no/unknownを必ず出す：Evidence IDで根拠を固定）

## 1. 目的（本編STEPへの地続き）
本テンプレは、STEP5のDFIR章を「推測」ではなく「Evidenceに基づく結論」に固定する。

- Hopごとに `observed_by_blue: yes/no/unknown` を必ず出す
- 根拠を Evidence ID（E-###）で追跡できるようにする
- unknown を許容しつつ、必ず “ギャップと改善提案” に落とす
- REW/CC（STEP5-14）に繋がる “要求” を明文化する

---

## 2. 運用ルール（固定）
### 2-1. 章の単位
- 原則：Hop単位（Hop1〜7）
- 補助：重大イベント単位（例：Consent付与、Role付与、横展開成功、API取得）

### 2-2. 結論の三択（定義）
- `yes`：Blue側で **追跡可能な根拠ログ** があり、相関できる（相関キー or 時刻・主体・対象で十分）
- `no`：イベントは起きたが **Blue側で観測できない**（監査/検知/収集のいずれにも痕跡が無い、または設計上取れない）
- `unknown`：ログ欠落/権限不足/保持期限/抽出経路未確立で **判断不能**

禁止：
- 結論を “たぶん” “おそらく” にしない  
  → unknown を使う

### 2-3. DFIR章は必ず“5点セット”
1) observed_by_blue（yes/no/unknown）
2) evidence_basis（E-### または log_not_available）
3) what_blue_can_see（追跡可能な最小要素）
4) gaps（不足ログ・相関キー・保持）
5) recommendation（改善提案：具体アクション）

---

## 3. DFIR結論テンプレ（Hop単位：コピペ用）

### 3-1. 最小版（必須）
~~~~
DFIR (HopN: <hop_name>):
- observed_by_blue: yes|no|unknown
- evidence_basis: E-###;E-### | log_not_available
- event_scope: (何のイベントについての結論か：signin / ldap_discovery / smb_session / rdp_logon / role_assign / api_access 等)
- time_window: YYYY-MM-DDTHH:MM:SS+09:00 ± (15m|1h|24h)
- correlation_key: (request_id/eventRecordId/session_id/none)

- what_blue_can_see:
  - (最小で：誰が/どこで/何をした が追えるか)

- gaps:
  - (不足ログ、保持不足、権限不足、相関キー欠落)

- recommendation:
  - (具体アクション：ログ取得/保持/相関キー/アラート/連携)
~~~~

### 3-2. 詳細版（推奨：Report提出向け）
~~~~
DFIR (HopN: <hop_name>):

conclusion:
  observed_by_blue: yes|no|unknown
  rationale_1line: (なぜその結論か：根拠ログがある/ない/取れない)
  confidence: high|medium|low
  claim_impact: (L1/L2/L3主張にどう影響するか)

evidence:
  evidence_basis:
    - evidence_id: E-###
      type: cloud-audit|auth-log|lm-log|edr|siem|http-evid|other
      note: (このEvidenceが示すもの)
  time_window:
    start_iso_jst: YYYY-MM-DDTHH:MM:SS+09:00
    end_iso_jst: YYYY-MM-DDTHH:MM:SS+09:00
  correlation:
    keys:
      - type: request_id|correlation_id|eventRecordId|session_id|trace_id|none
        value: <REDACTED_OR_ID>
    method: (相関方法：key突合 / 時刻±1h＋主体＋対象 / その他)

what_blue_can_see:
  minimal_traceability:
    - who: (主体：user/role/device のうち追えるもの)
    - where: (対象：app/resource/host/tenant)
    - what: (操作種別：signin/role_assign/api_access/lateral_move 等)
    - outcome: (success/fail)
  observability_notes:
    - (ログに残るがアラートは無い、などの区別も明記)

gaps:
  log_gaps:
    - (例：Sign-inログはあるがAuditログが無い、EDRが未導入、など)
  retention_gaps:
    - (保持期間、エクスポート不可、など)
  correlation_gaps:
    - (request_idが欠ける、時刻ズレ、など)
  operational_gaps:
    - (抽出担当不明、権限不足、SIEM未連携、など)

recommendations:
  immediate (during engagement):
    - action: (例：対象期間のログをエクスポートして提供)
      owner: client|tester|both
      deadline: (保持期限/報告会まで等)
      benefit: (DFIR結論の確度向上、L2主張成立 等)
  mid_term (post engagement):
    - action: (例：Sign-in/AuditのSIEM転送、相関ID保持)
      owner: client
      benefit: (恒久的な追跡性向上)
  detection_engineering (optional):
    - action: (例：特定イベントに対するアラート条件の追加)
      owner: client
      note: (検知と監査の違いを踏まえた提案)
~~~~

---

## 4. “yes/no/unknown” の判定ガイド（簡易）
### yes になり得る条件（例）
- CLOUD-AUDIT で当該操作が記録され、correlation_id が取れている
- LM-LOG で当該ログオンが記録され、eventRecordId が取れている
- EDR/SIEM で当該イベントが追跡でき、アラート/テレメトリの根拠がある

### no になり得る条件（例）
- イベントは実行されたが、監査設定/収集設計上、痕跡が残らないことが確認できる
- ログは存在するが収集されておらず、保持も無く、追跡できないことが確定

### unknown になる条件（例）
- 抽出権限がない
- 保持期限で既に消えている/消えそう
- 抽出経路（誰がどこからどう取るか）が未確立

---

## 5. 11_tools-lab / 06 との接続（実務上の要点）
- Run Log（11/02）の `correlation_key` を埋めないと DFIR は弱くなる  
- 06（観測点/相関キー）に従い、Hop別に “最低限見るログ” を押さえる  
- unknown が出たら、REW/CC（STEP5-14）で顧客に要求を出す（保持/権限/抽出）

---

## 6. 次のファイル（BASICS08-02）への接続
次は、Hop別に「Primary/Secondary のログソース」を決め打ちし、どこから何を取るかを固定する。

- 次：keda-lab/08_blue-dfir/02_log-sources-by-hop.md
  - Hop1〜7：Primary/Secondary ログソース
  - 取得フィールド（最小）
  - 相関キー（優先順位）
  - 欠落時のREW/CCトリガ

<<<END>>>
