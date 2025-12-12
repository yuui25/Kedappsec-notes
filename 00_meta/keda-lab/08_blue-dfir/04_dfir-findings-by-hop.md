<<<BEGIN>>>
# 08_blue-dfir / BASICS08-04 Hop別DFIR Findings生成（Evidence束ね→yes/no/unknown確定→gaps/reco→REW/CC連動）

## 1. 目的（本編STEPへの地続き）
本ファイルは、BASICS08-01（結論テンプレ）と BASICS08-02（ログソース決め打ち）、BASICS08-03（Evidence Pack）を前提に、
**Hop別DFIR章（Findings）を“機械的に”作るための生成ルール** を固定する。

- Hopごとに Evidence（E-###）を束ねて `observed_by_blue: yes/no/unknown` を確定
- “結論→根拠→見えること→ギャップ→提案” を **必ず5点セット** で出力
- unknown を許容しつつ、必ず **REW/CC（STEP5-14）要求** と **改善提案** に落とす
- 05_pentest-chain（STEP5）のDFIR章にそのまま貼れる粒度に整形する

接続先：
- STEP5（05_pentest-chain）：DFIR章（Hop1〜7）に直結
- 11_tools-lab：Run Log（correlation_key・evidence_id）から Findings を作る
- 06_os-network-protocol：相関キーの弱さ（時刻相関のみ）を unknown として露出させ、改善要求へ

---

## 2. 入力（必須成果物）
Hop別Findingsを作るには、最低限以下が揃っていること。

### 2-1. 必須入力
- Evidence Pack（BASICS08-03）
  - E-###_raw
  - E-###_normalized.csv（common schema）
  - E-###_note.md
- Hop定義（BASICS08-02 の Hop1〜7）
- Run Log（11_tools-lab）※推奨
  - hop / event_scope / evidence_id / correlation_key / time_window

### 2-2. 任意入力（あると “yes” を作りやすい）
- SIEM相関結果（cross-source correlation）
- 変更管理/運用チケット（正当変更の否定・確認）
- アーキ図（観測点の穴＝no/unknown の根拠化）

---

## 3. 生成プロセス（固定：Hopごとに5ステップ）
HopN ごとに、以下の順で “結論を確定” する。

### Step 1：対象イベントの宣言（event_scope固定）
- HopN で結論を出す対象を `event_scope` として宣言する  
  例：signin / ldap_discovery / smb_session / rdp_logon / role_assign / consent_grant / api_access / data_export / log_tamper

### Step 2：Evidence候補の収集（E-###束ね）
- normalized.csv を hop=HopN（または multi で HopN を含む）でフィルタ
- note.md の Hop mapping を見て、HopNに関係する E-### を列挙
- Evidence は以下の2種類に分けて扱う
  - Supporting：主張を直接支える（Primaryログ相当）
  - Corroborating：補強（Secondaryログ相当、否定/範囲確定/相関強化）

### Step 3：相関強度の評価（correlation tier）
Hopの結論は “相関強度” に依存する。以下を固定する。

- Tier-1（強）：request_id / correlation_id / operation_id 等で横断相関できる
- Tier-2（中）：session_id / logon_id 等で同一セッション束ねできる
- Tier-3（弱）：eventRecordId 等の単点固定はできるが横断相関が弱い
- Tier-4（最弱）：時刻±Δ + actor + target のみ（最後の手段）

### Step 4：observed_by_blue の確定（yes/no/unknown）
BASICS08-01 の定義を “判定アルゴリズム” として固定する。

- yes：Supporting Evidence が存在し、Tier-1〜2 の相関が成立する  
  例外：Tier-3のみでも、同一ログソース内で “誰が/どこで/何を/結果” が十分固定できる場合は yes 可
- unknown：ログ欠落/権限不足/保持期限/抽出経路未確立、または Tier-4 のみで相関が弱い
- no：イベントは起きた（または起き得る）が、Blue側設計として **観測できないことが確定**（根拠が必要）
  - no を出すには “観測不能である設計根拠” を Evidence（E-###）または明文化（log_not_availableの理由）で固定する

禁止：
- “たぶんyes” “おそらくno” は不可 → unknown を使う

### Step 5：gaps → recommendations → REW/CC 連動
unknown/no の場合は必ず以下へ落とす。
- gaps：不足ログ/保持/相関キー/運用（BASICS08-01 の分類）
- recommendations：具体アクション（immediate / mid-term / detection）
- REW/CC：Trigger（A〜E）を付与し、要求テンプレを生成（BASICS08-02/03）

---

## 4. 出力フォーマット（固定：Hop Findings テンプレ）
### 4-1. Hop Findings（Report貼り付け版：推奨）
~~~~
DFIR Finding (HopN: <hop_name>):
- event_scope: <scope>
- observed_by_blue: yes|no|unknown
- confidence: high|medium|low
- correlation_tier: Tier-1|Tier-2|Tier-3|Tier-4
- time_window: YYYY-MM-DDTHH:MM:SS+09:00 ± (15m|1h|24h)

- evidence_basis:
  - Supporting:
    - E-### (short note)
    - E-###
  - Corroborating:
    - E-###
    - E-###
  - log_not_available: (if any)

- what_blue_can_see:
  - who: <actor>
  - where: <target/resource/host/tenant>
  - what: <action/operation>
  - outcome: <success/fail/unknown>
  - trace: <correlation_key_type=value or "none">

- gaps:
  - log_gaps:
    - ...
  - retention_gaps:
    - ...
  - correlation_gaps:
    - ...
  - operational_gaps:
    - ...

- recommendations:
  - immediate:
    - action: ...
      owner: client|tester|both
      deadline: ...
      benefit: ...
  - mid_term:
    - action: ...
      owner: client
      benefit: ...
  - detection_engineering (optional):
    - action: ...
      owner: client
      note: ...
~~~~

### 4-2. Hop Findings（最小版：BASICS08-01の5点セット互換）
~~~~
DFIR (HopN: <hop_name>):
- observed_by_blue: yes|no|unknown
- evidence_basis: E-###;E-### | log_not_available
- event_scope: <...>
- time_window: <...>
- correlation_key: <...>

- what_blue_can_see:
  - ...

- gaps:
  - ...

- recommendation:
  - ...
~~~~

---

## 5. confidence（確度）決定ルール（固定）
confidence は “印象” で決めず、Evidenceの品質と相関で決める。

### 5-1. confidence決定マトリクス
- high：
  - Supporting Evidence が Primary 相当で、Tier-1〜2 の相関がある
  - かつ time_window が狭い（±1h以下が基本）
- medium：
  - Supporting があるが Tier-3、または Secondary中心で補強している
  - または time_window が広い（±24h等）
- low：
  - Tier-4（時刻相関のみ）に依存
  - またはログ欠落・抽出権限不足などで “部分的にしか見えない”

### 5-2. observed_by_blue と confidence の整合
- observed_by_blue=yes でも confidence=low はあり得る（例：時刻相関しかないが単点としては見える）
- observed_by_blue=unknown の場合、confidence は原則 low（例外：一部証跡が強いが最終判断に必要ログが欠落）

---

## 6. Evidence束ねルール（固定：Supporting/Corroborating）
### 6-1. Supporting に入れる条件（原則）
- BASICS08-02 の Primary に相当
- Hopの主張を直接示す（例：Role付与の監査、サインイン成功の記録、RDPログオンイベント等）

### 6-2. Corroborating に入れる条件（原則）
- 範囲確定（どれだけ/どこまで/何件）
- 否定材料（正当変更の可能性を潰す）
- 相関強化（別ソースの一致で Tier を上げる）

### 6-3. Evidenceが多すぎる場合の圧縮
- Supporting は最大3件を目安（超える場合は “代表Evidence” を選び、残りは appendix へ）
- Corroborating は “相関を強くするもの” を優先（ただの類似ログは削る）

---

## 7. yes/no/unknown の “落とし穴” 対策（固定）
### 7-1. “ログがある” ≠ yes
- ログが単体で存在しても、相関できない（Tier-4のみ）なら unknown を基本とする
- yes に倒す場合は “なぜ相関不要で足りるか” を rationale に書ける状態にする（noteで固定）

### 7-2. no を出すには根拠が要る
- no は “取れなかった” ではなく “設計上観測できないことが確定”  
  → 設計根拠（監査未対応、製品仕様、ログ非対象、等）を Evidence か明文化で固定

### 7-3. unknown を “改善要求” に落とさないのは禁止
- unknown のまま報告書に残す場合、必ず gaps と recommendations と REW/CC をセットで出す

---

## 8. REW/CC（STEP5-14）への連動（固定）
Hop Findings 内で “Trigger” を明示し、要求を Evidence ID と同様に追跡する。

### 8-1. Trigger の付与ルール（再掲）
- Trigger-A：Primaryログ無し/無効
- Trigger-B：保持期限切れ
- Trigger-C：抽出権限/抽出担当不明
- Trigger-D：相関キー欠落
- Trigger-E：SIEM未連携（横断相関不可）

### 8-2. Hop Findings への埋め込み（推奨）
~~~~
- rew_cc:
  - trigger: Trigger-D
  - request_ref: REQ-### (if used)
  - requested_log_source: <...>
  - deadline: <...>
~~~~

（REQ-### は案件内通し番号。Evidenceと同様に上書き禁止）

---

## 9. 生成例（短縮：Hop2 認証）
~~~~
DFIR Finding (Hop2: Authentication):
- event_scope: signin
- observed_by_blue: yes
- confidence: high
- correlation_tier: Tier-1
- time_window: 2025-12-10T10:00:00+09:00 ± 1h

- evidence_basis:
  - Supporting:
    - E-012 (IdP sign-in log shows successful sign-in for userA with correlation_id)
  - Corroborating:
    - E-013 (MFA detail confirms push approval for same correlation_id)

- what_blue_can_see:
  - who: userA
  - where: app=PortalX / tenant=TenantY
  - what: sign-in (interactive)
  - outcome: success
  - trace: correlation_id=<REDACTED>

- gaps:
  - correlation_gaps:
    - none

- recommendations:
  - immediate:
    - action: Export sign-in logs for the whole engagement window to preserve correlation_id
      owner: client
      deadline: before report-out
      benefit: Prevent future unknown due to retention
~~~~

---

## 10. 次のファイル（BASICS08-05）への接続
次は、Hop別Findings（BASICS08-04）を **報告書の“DFIR章”構造** に落とし込み、本文（主張）と Evidence（E-###）の参照を固定する。
また、DFIR観測ギャップを “改善ロードマップ” として束ねる。

- 次：keda-lab/08_blue-dfir/05_dfir-report-assembly.md
  - DFIR章の章立て（Hop順＋重大イベント）
  - Evidence参照の書式統一（本文→E-###→付録）
  - unknown の扱い（REW/CC要求→改善提案→優先度付け）
  - 顧客への提示テンプレ（改善ロードマップ：30/90/180日）
<<<END>>>
