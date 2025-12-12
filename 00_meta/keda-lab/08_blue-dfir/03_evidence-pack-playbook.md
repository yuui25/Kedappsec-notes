<<<BEGIN>>>
# 08_blue-dfir / BASICS08-03 Evidence Pack Playbook（抽出→正規化→採番→提出の手順固定）

## 1. 目的（本編STEPへの地続き）
本ファイルは、BASICS08-01（DFIR結論テンプレ）と BASICS08-02（Hop別ログソース決め打ち）を **実務運用できる手順** に落とす。

- 監査・認証・ホスト・ネットワークのログを **Evidence Pack（E-###）** として採取・固定
- 正規化（common schema）により、Hop別の「yes/no/unknown」を **Evidence ID で裏取り可能** にする
- 欠落があれば自動的に REW/CC（STEP5-14）要求へ落とす（放置禁止）
- 11_tools-lab の Run Log と連動し、相関キーの欠落を **工程の早い段階で露出** させる

接続先：
- 05_pentest-chain（STEP5）：DFIR章の主張（observed_by_blue）を Evidence で固定
- 06_os-network-protocol：観測点×相関キーの設計（最後の手段に頼らない）
- 11_tools-lab：Run Log / Evidence Pack の運用（採番・提出物・再現性）

---

## 2. Evidence Pack の定義（固定）
Evidence Pack は **「生ログ」＋「正規化」＋「ノート」** の3点セットで、Evidence ID（E-###）で管理する。

### 2-1. Evidence Pack 構成（固定）
- E-###_raw.(csv|json|evtx|gz|zip)
  - 抽出元から得た **生ログ**（改変せず保管）
- E-###_normalized.csv
  - common schema に合わせた **最小フィールド正規化**
- E-###_note.md
  - 抽出条件・示す主張・相関キー・不足点を **1枚で説明**（報告書に貼れる）

### 2-2. Evidence ID（E-###）採番ルール（固定）
- E-### は **案件単位の通し番号**（例：E-001〜）
- 採番の単位は「ログソース×抽出条件×時間窓」
  - 例：Entra Sign-in（Hop2）を “対象ユーザA、期間X” で抽出 → 1 Evidence
- 1 Evidence に複数Hopが混ざる場合は許容するが、E-###_note.md で **Hop対応表** を必ず書く

禁止：
- 同じ E-### を上書き再利用しない（差分は E-### を新規採番）

---

## 3. 抽出フロー（3ルート固定）
抽出方法は、現場の制約により 3ルートに分類し、どれでも Evidence Pack を作れるようにする。

### 3-1. ルートA：Portal/Console Export（UI抽出）
- 対象：IdP / Cloud / SaaS 監査ログ（UIが提供される場合）
- 注意：UI抽出はフィールド欠落が起きやすい（可能ならAPIと二重化）

### 3-2. ルートB：API Export（API抽出）
- 対象：IdP / Cloud / SaaS / EDR（APIで詳細が取れる場合）
- 利点：相関IDや内部IDが取れやすい（DFIRの “yes” を作りやすい）

### 3-3. ルートC：SIEM/Log Lake Query（集約基盤抽出）
- 対象：SIEM（KQL/Splunk等）に集約済みログ
- 注意：集約時にフィールドが落ちることがある（rawが必要なら原本へ戻る）

---

## 4. common schema（正規化カラム固定）
BASICS08-02 の「最小フィールド」を、Evidence Pack の normalized.csv に **必ず揃える**。

### 4-1. normalized.csv の固定カラム（最低限）
- evidence_id（例：E-012）
- hop（Hop1..Hop7 / multi）
- timestamp_iso_jst（YYYY-MM-DDTHH:MM:SS+09:00）
- source_type（cloud-audit|auth-log|lm-log|edr|siem|http-edge|dns|other）
- actor（user/servicePrincipal/role/device の識別子）
- actor_type（user|spn|role|device|unknown）
- source（src_ip|src_host|device_id のうち最低1つ）
- target（resource|app|host|object_id のうち最低1つ）
- action（operation/event_name）
- outcome（success|fail|unknown）
- correlation_key_type（request_id|correlation_id|operation_id|session_id|logon_id|eventRecordId|none）
- correlation_key_value（ID値 or REDACT）
- raw_pointer（raw内の行番号/イベントID/検索クエリなど：再現性のため）
- note（短い補足：1行）

### 4-2. タイムゾーン固定ルール
- すべて JST（+09:00）へ正規化
- raw が UTC の場合は note に「UTC→JST変換済」を明記
- 時刻精度が粗い（分単位など）場合は outcome を下げず、confidence に反映（BASICS08-01 詳細版）

---

## 5. Evidence Note（E-###_note.md）テンプレ（固定）
Reportへ貼れることを前提に、ノートは **短く・相関に強く** 書く。

~~~~
# Evidence Note: E-### (source: <log_source_name>)

## Summary (1-3 lines)
- This evidence supports: (HopN / event_scope)
- Why it matters: (observed_by_blue を yes/no/unknown のどれに寄与するか)

## Extraction
- route: A Portal | B API | C SIEM
- extractor: client | tester | both
- time_window_jst: YYYY-MM-DDTHH:MM+09:00 ~ YYYY-MM-DDTHH:MM+09:00
- filter:
  - actor: <user/spn/role>
  - target: <app/resource/host/object>
  - action: <signin/role_assign/api_access/...>
- export_format: csv|json|evtx|...

## Correlation
- primary_key: (type=value)
- secondary_keys:
  - (type=value)
- correlation_method:
  - key match | time±Δ + actor + target | other

## What it shows (bullet)
- (誰が / どこで / 何を / 結果)

## Gaps / Risks
- log_gap:
- retention_gap:
- correlation_gap:
- operational_gap:

## Hop mapping
- Hop2: <reason>
- Hop5: <reason>
~~~~

---

## 6. Hop別：Evidence Pack 作成手順（実務手順固定）
BASICS08-02 の Primary/Secondary を “必ず” Evidence 化するため、Hopごとに「作るべき Evidence」を定義する。

### 6-1. Hop1（入口）Evidence 作成手順
**作るべき Evidence（最小）**
- E-###：HTTP edge（WAF/CDN/LB）アクセスログ（Primary）
- E-###：Web server / reverse proxy access（Secondary か、Primary欠落時の代替）

**抽出の固定手順**
1) time_window を決める（起点イベントが不明なら ±24h、特定できるなら ±1h）
2) フィルタ：host + path + src_ip（候補）+ user_agent（候補）
3) correlation_key がある場合は最優先で含める（request_id/trace_id）
4) raw → normalized → note を作る

**Quality Gate（合格条件）**
- timestamp / src_ip / host / path が揃う
- request_id が取れる場合は normalized に必ず入る

**欠落時 REW/CC トリガ**
- Trigger-A：edgeログがない（Primary欠落）
- Trigger-D：request_id が採番されず、アプリ側と相関不能

---

### 6-2. Hop2（認証）Evidence 作成手順
**作るべき Evidence（最小）**
- E-###：IdP sign-in（Primary）
- E-###：MFA/CA詳細（Secondary、可能なら同一抽出で統合）

**抽出の固定手順**
1) actor（ユーザ/アカウント）を確定（不明なら候補リストで抽出）
2) 成否（success/fail）を含める
3) correlation_id / session_id を可能な限り取得
4) device/場所情報（src_ip, device_id）があれば入れる

**Quality Gate**
- actor / outcome / src_ip / app が揃う
- correlation_id が取れるなら必須

**欠落時 REW/CC**
- Trigger-B：保持期限切れ（過去ログが取れない）
- Trigger-C：抽出権限不足（管理者権限が必要）

---

### 6-3. Hop3（内部探索）Evidence 作成手順
**作るべき Evidence（最小）**
- E-###：DC Security（Primary候補）
- E-###：EDR/Sysmon（PrimaryまたはSecondary）

**抽出の固定手順**
1) host（端末/サーバ）を確定（Hop2 の device 情報を使う）
2) LDAP/DNS/SMB列挙の “兆候” を拾う（プロセス/通信/認証）
3) eventRecordId / logon_id 等、束ねキーを確保

**Quality Gate**
- host + actor + 目的先（DC/LDAP/DNS）が追える
- eventRecordId で単点固定できる

**欠落時 REW/CC**
- Trigger-A：DC監査が無効
- Trigger-A：EDR未導入範囲が広い（観測穴）

---

### 6-4. Hop4（横展開）Evidence 作成手順
**作るべき Evidence（最小）**
- E-###：対象ホスト Security log（ログオン/リモート）（Primary）
- E-###：EDR（remote exec/横展開ツール痕跡）（Secondary）

**抽出の固定手順**
1) src_host → dst_host の候補を列挙（Hop3の探索結果を使う）
2) 445/3389/5985 等の接続兆候 + ログオンイベントを突合
3) logon_type / logon_id を最優先で確保

**Quality Gate**
- src/dst/actor が揃う（最低限）
- logon_id で同一セッション束ね可能

**欠落時 REW/CC**
- Trigger-B：ホストログ保持が短い
- Trigger-D：ログオンIDが取れず相関が弱い

---

### 6-5. Hop5（権限上昇・永続化）Evidence 作成手順
**作るべき Evidence（最小）**
- E-###：Cloud/SaaS Audit（Role/Consent/Policy） （Primary）
- E-###：IdP 管理操作ログ（Secondary）

**抽出の固定手順**
1) operation（role_assign / consent_grant / policy_change 等）でフィルタ
2) actor と target_object_id を固定
3) operation_id / correlation_id を確保

**Quality Gate**
- actor + operation + target が揃う
- operation_id が取れる（取れない場合は “unknown” に寄りやすい）

**欠落時 REW/CC**
- Trigger-A：監査ログが未有効
- Trigger-E：SIEM未連携で横断相関不可

---

### 6-6. Hop6（目的達成）Evidence 作成手順
**作るべき Evidence（最小）**
- E-###：対象SaaS/アプリ監査（download/export/read） （Primary）
- E-###：DLP/CASB/Proxy（外部送信） （Secondary）

**抽出の固定手順**
1) object（ファイル/メール/DB）と action を固定
2) volume（件数/バイト）が取れるなら必ず入れる
3) 外部送信経路（proxy/egress）を補強Evidenceとして作る

**Quality Gate**
- actor + object + action + outcome が揃う
- volume が取れる場合は必須

**欠落時 REW/CC**
- Trigger-A：閲覧/取得が監査されない設計（no判定の根拠化が必要）
- Trigger-D：object_id が取れず範囲確定ができない

---

### 6-7. Hop7（痕跡操作・影響）Evidence 作成手順
**作るべき Evidence（最小）**
- E-###：監査設定変更（ログ無効化/保持変更/転送停止） （Primary）
- E-###：EDR（停止/ログ削除/暗号化） （Secondary）

**抽出の固定手順**
1) “ログ・監査・転送・保持” に関する operation を最優先で抽出
2) before/after（変更前後）が取れるなら note に必ず書く
3) 監査停止が疑われる場合、SIEM取り込みメトリクスも補助で作る

**Quality Gate**
- actor + change + target が揃う
- change_id / operation_id で固定できる

**欠落時 REW/CC**
- Trigger-A：監査停止でログ自体が残らない（unknown を確定させ、改善要求へ）
- Trigger-E：取り込み停止の可視化がない

---

## 7. 提出物チェック（Quality Gates：全Evidence共通）
Evidence Pack を “DFIRの根拠” として成立させるための、共通合格基準。

### 7-1. 必須チェック（Failなら unknown へ倒す）
- raw が存在する（生ログ不在は禁止）
- normalized に timestamp_iso_jst / actor / action / outcome がある
- correlation_key が none の場合、note に「なぜ取れないか」と「代替相関」を明記
- time_window が note に明記され、抽出条件が再現可能

### 7-2. 相関強度チェック（弱い場合の扱い）
- key相関（request_id/correlation_id/operation_id）あり → DFIR yes の根拠になりやすい
- 時刻相関のみ → DFIR は原則 unknown（補強Evidenceが揃ったら再評価）

---

## 8. Run Log（11_tools-lab）への記録（連動ルール）
Evidence を作ったら、同じタイミングで Run Log に追記し、DFIR章の確度を上げる。

### 8-1. Run Log に必ず入れる項目（固定）
- hop, event_scope, evidence_id（E-###）
- correlation_key（type/value）
- time_window
- 抽出ルート（A/B/C）と抽出者（client/tester/both）
- 欠落があれば Trigger（A〜E）と REW/CC 要求ID（後続で採番）

---

## 9. Annex：抽出クエリ例（雛形：適用時に具体化）
※ここは “手順の型” を示す。実環境の製品名に合わせて置換する。

### 9-1. SIEMクエリ雛形（共通）
~~~~
# Query template (SIEM)
time: [start_jst, end_jst]
filter:
  actor contains "<user_or_spn>"
  action in ("<operation_1>","<operation_2>")
  target contains "<resource_or_host>"
project:
  timestamp, actor, action, target, outcome, correlation_id, src_ip, device_id
~~~~

### 9-2. Hostログ抽出雛形（共通）
~~~~
# Host log extraction template
scope:
  host: <hostname>
  time_window: <start> - <end>
  events:
    - logon / remote logon
    - process start
    - network connection (if available)
output:
  raw: evtx
  normalized: csv (common schema)
~~~~

---

## 10. 次のファイル（BASICS08-04）への接続
次は、Evidence Pack を “Hop別DFIR結論（BASICS08-01）” に **機械的に貼り付けられる** 形式へ変換する（結論生成の型）。

- 次：keda-lab/08_blue-dfir/04_dfir-findings-by-hop.md
  - Hopごとに evidence_basis（E-###）を束ねるルール
  - observed_by_blue の判定（yes/no/unknown）を Evidence から確定する手順
  - unknown の場合の gaps → recommendations への落とし込み（REW/CC連動）
<<<END>>>
