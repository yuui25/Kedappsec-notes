<<<BEGIN>>>
# 08_blue-dfir / BASICS08-05 DFIRレポート組み立て（章立て固定・Evidence参照統一・unknownの改善ロードマップ化）

## 1. 目的（本編STEPへの地続き）
本ファイルは、BASICS08-04 の Hop別Findings を入力として、STEP5（05_pentest-chain）の報告書に貼れる
**DFIR章（Observability/Detection/Responseの結論）を“組み立てる”手順と書式** を固定する。

- DFIR章を Hop1〜7（＋重大イベント）で章立てし、Evidence（E-###）参照を統一する
- yes/no/unknown を **推測ではなく Evidence で固定**し、主張の強度（confidence）も一貫させる
- unknown/no を “放置” せず、REW/CC（STEP5-14）要求へ落とし、さらに **改善ロードマップ（30/90/180日）** に束ねる
- 「検知できた/できない」だけでなく、**追跡可能性（traceability）** と **運用可能性（operability）** を結論として提示する

接続先：
- STEP5（05_pentest-chain）：レポート本文（攻撃チェーン）× DFIR章（観測・検知・対応）の統合
- BASICS08-01〜04：結論（yes/no/unknown）・ログソース・Evidence Pack・Findings
- 11_tools-lab：Run Log から Evidence と Findings の整合を担保
- 06_os-network-protocol：観測点ギャップの説明（なぜ unknown/no になるかの根拠）

---

## 2. DFIR章の設計思想（固定）
### 2-1. DFIR章は “攻撃があったか” を断定する章ではない
- DFIR章の主目的は **Blue側で追跡・検知・対応できたか** を Hop別に評価し、改善点を具体化すること。
- 攻撃実在の断定は、別章（攻撃チェーン本文・PoC）と相互参照する。

### 2-2. yes/no/unknown は “監査・収集・相関” の成熟度指標
- yes：相関可能な根拠ログがあり、追跡線が引ける
- no：設計上観測できない（根拠必須）
- unknown：欠落・権限・保持・抽出経路不備で判断不能（改善要求に落とす前提）

---

## 3. DFIR章の章立て（固定テンプレ）
### 3-1. 章のトップ構成（必須）
1) Executive DFIR Summary（1ページ以内）
2) Hop別 Findings（Hop1〜7）
3) Critical Events（重大イベント：Role/Consent/MFA変更等）
4) Observability Gap Register（unknown/no の一覧）
5) Improvement Roadmap（30/90/180日）
6) Evidence Index（E-###一覧と所在）
7) Appendices（raw/normalized/note 参照）

### 3-2. Executive DFIR Summary に必ず入れる要素
- 全Hopの observed_by_blue 集計（yes/no/unknown の数と比率）
- 最も重大な unknown（攻撃チェーン主張に影響するもの上位3）
- “すぐ直せる改善” 上位3（30日内）
- “構造的改善” 上位3（90〜180日）

---

## 4. Evidence参照の書式統一（固定）
### 4-1. 本文での参照記法（固定）
- 参照は必ず Evidence ID を使う：`[E-012]` の形式
- 複数参照：`[E-012, E-013]`
- “ログが無い” は Evidence ID で誤魔化さない：`[log_not_available: <reason>]`

禁止：
- “ログを確認したが〜” のように Evidence ID が無い記述

### 4-2. Findings → Evidence Pack への対応（固定）
- Hop Findings の evidence_basis にある E-### は、Evidence Index で必ず引ける
- Evidence Index は以下を最低限持つ（後述）

---

## 5. Hop別セクションの組み立てルール（固定）
Hop別は BASICS08-04 の Hop Findings を **そのまま貼るのではなく**、報告書向けに “読みやすく” 組み替える。

### 5-1. Hop別セクションの固定構造
1) Hop要約（1〜3行）
2) observed_by_blue / confidence / correlation_tier（表形式でも可）
3) what_blue_can_see（who/where/what/outcome/trace）
4) evidence_basis（Supporting/Corroborating）
5) gaps（カテゴリ別）
6) recommendations（immediate/mid-term/detection）
7) REW/CC（該当する場合のみ：Triggerと要求）

### 5-2. “報告書化” のための短文化ルール
- evidence_basis の “短い説明” は E-###_note.md の Summary を流用
- what_blue_can_see は “追跡の最小要素” だけに絞る（詳細は appendix）
- gaps は “原因” を先に、recommendations は “具体アクション” を先に書く

---

## 6. Critical Events（重大イベント）章の作り方（固定）
Hopとは別に、顧客が意思決定しやすい重大イベントを横串でまとめる。

### 6-1. 重大イベントの対象（例）
- Role付与 / 権限変更（Hop5）
- Consent付与（Hop5）
- MFA設定変更（Hop2）
- Mailbox rule / Forwarding（Hop6）
- Logging/Retention/Forwarding変更（Hop7）
- APIキー取得・使用（Hop2/6）

### 6-2. 重大イベントの固定テンプレ
~~~~
Critical Event: <event_name>
- observed_by_blue: yes|no|unknown
- evidence_basis: [E-###, ...] | [log_not_available: ...]
- impact_on_chain: (攻撃チェーン主張に与える影響)
- what_blue_can_see: (追える最小要素)
- gaps: ...
- recommendation: ...
~~~~

---

## 7. Observability Gap Register（unknown/no の台帳化）
unknown/no を “改善に繋がる管理対象” にするため、必ず台帳に落とす。

### 7-1. 台帳の固定カラム
- gap_id: GAP-###
- hop: HopN
- event_scope
- observed_by_blue: unknown|no
- trigger: Trigger-A|B|C|D|E
- missing_component: (log_source / field / retention / access)
- consequence: (どの主張が弱くなるか)
- evidence_ref: [E-###] | [log_not_available]
- recommendation_ref: (Roadmap項目IDにリンク)

### 7-2. 台帳の運用ルール
- unknown/no が1件でもあれば GAP-### を必ず採番
- GAP-### は Roadmap の改善項目（IMP-###）に必ず紐付ける

---

## 8. Improvement Roadmap（30/90/180日）への落とし込み（固定）
### 8-1. ロードマップの目的
- unknown/no を減らし、yes を増やすための “投資対効果” を提示する
- “やること” を運用・設計・検知の3層で整理する

### 8-2. 改善項目（IMP-###）の固定テンプレ
~~~~
Improvement Item: IMP-###
- horizon: 30d|90d|180d
- category: logging|retention|correlation|siem_integration|detection|process|access
- related_gaps: GAP-###, GAP-###
- action: (具体アクション)
- owner: client|both
- effort: S|M|L
- benefit: (どのHopが yes になり得るか / 検知が可能になるか)
- acceptance_criteria: (完了条件：フィールドが取れる、保持がX日、相関IDが残る等)
- evidence_of_completion: (設定画面のスクショ/ログサンプル/テスト結果など)
~~~~

### 8-3. 優先順位付け（固定ルール）
- P0：Hop2（認証）/ Hop5（権限）/ Hop7（監査停止）に関わる unknown/no
- P1：Hop4（横展開）に関わる unknown（EDR/hostログ）
- P2：Hop1（入口）と Hop6（目的達成）の範囲確定

---

## 9. Evidence Index（E-###一覧）テンプレ（固定）
DFIR章の参照整合を担保するため、Evidence Index を必ず付ける。

### 9-1. 固定カラム
- evidence_id（E-###）
- source_type（cloud-audit/auth-log/edr/siem/http-edge/...）
- log_source_name（製品・ログ名）
- hop_mapping（Hop2, Hop5, ...）
- time_window_jst（start-end）
- correlation_key（type）
- contains_supporting_for（signin/role_assign/...）
- files:
  - raw: <filename>
  - normalized: <filename>
  - note: <filename>

---

## 10. 組み立て手順（実務：チェックリスト）
### 10-1. 手順（固定）
1) Hop1〜7 の Hop Findings を揃える（BASICS08-04）
2) Evidence Index を生成（E-###を全列挙）
3) Hop別セクションを組み立て（要約→追跡可能性→Evidence→gaps→提案）
4) 重大イベント章を生成（横串）
5) Gap Register（GAP-###採番）を作る
6) Roadmap（IMP-###採番）を作り、GAP-###とリンク
7) Executive DFIR Summary を最後に作る（全体集計から）

### 10-2. 最終整合チェック（Failなら戻る）
- Hop別セクションの observed_by_blue は Evidence で参照できるか
- Evidence ID 参照のない主張が存在しないか
- unknown/no は GAP-### と IMP-### に必ず落ちているか
- “no” の根拠が log_not_available の理由として明文化されているか

---

## 11. 次のファイル（BASICS08-06）への接続
次は、DFIR章で作った Gap/Roadmap を、STEP5-14（REW/CC）で顧客に要求する “契約・運用・提出物” の形に落とす。
つまり、改善提案を “実行可能な要求仕様” に変換する。

- 次：keda-lab/08_blue-dfir/06_rew-cc-requests-spec.md
  - REW/CC 要求の分類（ログ/保持/権限/連携/検知）
  - 要求テンプレ（REQ-###）と承認フロー
  - 提出物の形式（Evidence Pack互換）
  - 期限・優先度・責任分界（RACI）
<<<END>>>
