<<<BEGIN>>>
# 08_blue-dfir / BASICS08-09 Hop別オブザーバビリティマップ（観測点×ログ×相関キー×観測穴パターン固定）

## 1. 目的（本編STEPへの地続き）
本ファイルは、BASICS08-02（Hop別ログソース）と BASICS08-08（運用モデル）を補強し、
Hopごとに「どこで観測できるか（観測点）」を **アーキ図レベルで説明できる** ようにする。

- Hop×観測点（Network / Host / Identity / Cloud）を固定し、unknown/no の理由を説明可能にする
- Primary/Secondaryログを観測点へ割り当て、Evidence Pack（E-###）の作成先を明確化する
- 相関キー（Tier-1〜4）を観測点に紐付け、Tier-4（時刻相関のみ）へ落ちる原因を露出させる
- 観測穴パターンを “設計課題” として IMP/REQ に落とす（STEP5-14の要求化を容易にする）

接続先：
- BASICS08-01：yes/no/unknown の結論を観測点とEvidenceで固定
- BASICS08-02：ログソース決め打ちの “どこにあるか” を明確化
- BASICS08-04/05：Findings/Reportで unknown/no の理由説明に使う
- BASICS08-06/07：REQ/GAPの根拠（Trigger-A〜E）を技術的に裏付ける
- 06_os-network-protocol：観測点設計（どこで見るべきか）と整合

---

## 2. 観測点（Observation Planes）の定義（固定）
観測点は4面で固定し、Hop別に「最低どこか1面で見えるべき」を判断する。

- Network（N）：WAF/CDN/LB/Proxy/FW/NetFlow/DNS/NDR など “通信” の観測
- Host（H）：OSログ/EDR/Sysmon/プロセス/ログオン/ファイル操作など “端末” の観測
- Identity（I）：IdP/SSO/MFA/認証・認可イベントなど “主体” の観測
- Cloud（C）：Cloud/SaaS監査ログ、API監査、権限変更など “制御面” の観測

---

## 3. 相関キー（Tier-1〜4）と観測点の関係（固定）
### 3-1. Tier定義（再掲）
- Tier-1：request_id / trace_id / correlation_id / operation_id（横断相関が強い）
- Tier-2：session_id / logon_id（セッション束ねが可能）
- Tier-3：eventRecordId / object_id（単点固定は可能）
- Tier-4：時刻±Δ + actor + target（最後の手段）

### 3-2. 観測点別 “出やすいキー”（傾向）
- Network：request_id/trace_id（edge採番があればTier-1）、なければ時刻相関に落ちやすい
- Host：logon_id（Tier-2）、eventRecordId（Tier-3）
- Identity：correlation_id/session_id（Tier-1〜2）
- Cloud：operation_id/correlation_id/object_id（Tier-1〜3）

---

## 4. Hop別オブザーバビリティマップ（固定テーブル）
> 各Hopで “理想” を示しつつ、現実に穴が出る点をパターン化して後続（GAP/REQ）へ落とす。

### 4-1. Hop1（入口）
- 主体：外部→境界（Edge）
- 期待観測点：N（最優先）＋（可能なら）H（WAF背後のホスト）

**観測点マップ**
- N/Primary：WAF/CDN/LB Access Log（request_id/trace_idがあればTier-1）
- N/Secondary：Proxy/FW/NetFlow、DNS（到達性・反復）
- H/Secondary：Web server access、EDR（初回実行/初回通信）

**相関キー（優先）**
- Tier-1：request_id / trace_id
- Tier-4：時刻±15m + src_ip + host + path

**観測穴パターン（典型）**
- (A) WAF/CDN未導入 → NのPrimary欠落（Trigger-A）
- (D) request_idが採番されない → アプリ/IdPと相関不能（Trigger-D）
- (B) Edgeログ保持が短い → 過去期間が取れない（Trigger-B）

---

### 4-2. Hop2（認証）
- 主体：Identity（ユーザ/サービス）
- 期待観測点：I（最優先）＋C（SaaS側監査）

**観測点マップ**
- I/Primary：IdP Sign-in Log（correlation_id、MFA detail）
- I/Secondary：Conditional Access / MFAログ
- C/Secondary：SaaS Sign-in監査（アプリ側session_id）

**相関キー（優先）**
- Tier-1：correlation_id / request_id（IdP発行）
- Tier-2：session_id
- Tier-4：時刻±1h + user + src_ip + app

**観測穴パターン**
- (B) サインインログ保持切れ → unknown固定（Trigger-B）
- (C) 抽出権限が無い → unknown（Trigger-C）
- (E) SIEM未連携で横断相関ができない（Trigger-E）

---

### 4-3. Hop3（内部探索）
- 主体：端末→AD/内部サービス
- 期待観測点：H + I（Kerberos/LDAP） + N（内部DNS/SMB）

**観測点マップ**
- H/Primary：EDR/Sysmon（process/commandline/network）
- I/Primary候補：DC Security（Kerberos/LDAP関連、認証痕跡）
- N/Secondary：DNSログ、NetFlow/NDR（east-west）

**相関キー（優先）**
- Tier-2：logon_id（端末/認証）
- Tier-3：eventRecordId
- Tier-4：時刻±1h + host + user + dst

**観測穴パターン**
- (A) EDR未導入範囲が広い → H穴（Trigger-A）
- (A) DC監査が薄い → I穴（Trigger-A）
- (D) logon_id等が揃わず時刻相関に落ちる（Trigger-D）

---

### 4-4. Hop4（横展開）
- 主体：端末→端末（リモートログオン/実行）
- 期待観測点：H（対象ホスト）＋ N（445/3389/5985等）＋ I（認証）

**観測点マップ**
- H/Primary：対象ホスト Security Log（ログオン種別/成功失敗）
- H/Secondary：EDR（remote execツール、横展開）
- N/Secondary：FW/NetFlow/NDR
- I/Secondary：DC認証ログ（横展開時の認証）

**相関キー（優先）**
- Tier-2：logon_id
- Tier-3：eventRecordId
- Tier-4：時刻±15m + user + src_host + dst_host + port

**観測穴パターン**
- (B) ホストログ保持短い → unknown（Trigger-B）
- (A) 監査未設定（ログオンが取れない）→ no/unknown（Trigger-A）
- (D) 相関IDがなく “通信だけ/ログオンだけ” になる（Trigger-D）

---

### 4-5. Hop5（権限上昇・永続化）
- 主体：管理操作（制御面）
- 期待観測点：C（最優先）＋ I（IdP管理操作）

**観測点マップ**
- C/Primary：Cloud/SaaS Audit（role_assign/consent/policy_change）
- I/Secondary：IdP管理ログ（アプリ登録、権限付与）
- H/Secondary：管理ツール実行（EDR）

**相関キー（優先）**
- Tier-1：operation_id / correlation_id
- Tier-3：target_object_id
- Tier-4：時刻±1h + actor + operation + target

**観測穴パターン**
- (A) 監査ログ未有効化 → 重大unknown/no（Trigger-A）
- (E) SIEM未連携 → 横断調査ができない（Trigger-E）
- (D) operation_idが落ちる（集約で欠落）→ Tier低下（Trigger-D）

---

### 4-6. Hop6（目的達成）
- 主体：データ操作（DL/Export/変更/外部送信）
- 期待観測点：C（SaaS監査）＋ N（外部送信）＋ H（端末操作）

**観測点マップ**
- C/Primary：対象SaaS/アプリ監査（download/export/read）
- N/Secondary：Proxy/DLP/CASB（外部送信、容量）
- H/Secondary：EDR（圧縮/アップロード/ツール）

**相関キー（優先）**
- Tier-1：operation_id / download_session_id（あれば）
- Tier-3：object_id
- Tier-4：時刻±1h + actor + object + action

**観測穴パターン**
- (A) “閲覧/取得” が監査対象外 → no を根拠化（REQ-ARC）（Trigger-A）
- (D) object_id/volumeが取れず範囲確定不可（Trigger-D）
- (E) DLP/CASB未導入で外部送信が追えない（Trigger-A/E）

---

### 4-7. Hop7（痕跡操作・影響）
- 主体：防御回避/ログ停止/暗号化など
- 期待観測点：C（監査設定変更）＋ H（EDR）＋（可能なら）I（管理操作）

**観測点マップ**
- C/Primary：監査設定変更の監査（logging/retention/forwarding）
- H/Secondary：EDR（停止/ログ削除/暗号化）
- N/Secondary：SIEM取り込みメトリクス（欠測/遅延）

**相関キー（優先）**
- Tier-1：change_id / operation_id
- Tier-3：eventRecordId
- Tier-4：時刻±1h + actor + target + change

**観測穴パターン**
- (A) 監査停止が先に起き、ログが残らない → unknown確定＋改善要求（Trigger-A）
- (E) 取り込み停止の可視化がない → 影響範囲を説明できない（Trigger-E）

---

## 5. “観測穴パターン” → GAP/REQ への落とし込み（固定）
### 5-1. パターン→Trigger対応（固定）
- WAF/EDR/監査が “未導入・未有効” → Trigger-A（設計/設定）
- 保持が足りず過去が取れない → Trigger-B（RET）
- 抽出できない（権限/担当不明）→ Trigger-C（ACC/OPS）
- 相関キーが無い/落ちた → Trigger-D（COR/INT）
- SIEM未連携で横断相関不可 → Trigger-E（INT）

### 5-2. 生成ルール（固定）
- Hopごとに Trigger が出たら GAP-### を採番
- GAP-### は IMP-###（30/90/180日）へ必ず紐付け
- IMP-### は REQ-###（仕様）へ必ず落とす（実行可能化）

---

## 6. DFIR章（Report）での使い方（固定）
- Hop Findings の “gaps” に、この観測点マップの穴パターンを短く転記する
- “なぜ unknown/no なのか” は、Triggerと観測点（N/H/I/C）で説明する
- 改善提案（IMP/REQ）は「どの観測点に何を足すか」で語る（顧客が理解しやすい）

---

## 7. 次のファイル（BASICS08-10）への接続
次は、Hop別の観測点マップを “検知設計（Detection Engineering）” に繋げる。
つまり、yes（追跡可能）と検知（アラート）を混同せず、Hop別に
- 監査（Audit）
- 追跡（Trace）
- 検知（Detect）
- 対応（Respond）
を整理し、検知ユースケースを作る。

- 次：keda-lab/08_blue-dfir/10_detection-usecases-by-hop.md
  - Hop別：検知ユースケース（何をアラートにするか）
  - 必要ログと相関キー（Tier）
  - 誤検知/運用負荷の注意点
  - 段階的導入（30/90/180日）への紐付け
<<<END>>>
