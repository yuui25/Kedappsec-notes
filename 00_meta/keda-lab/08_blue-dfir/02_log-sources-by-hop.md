<<<BEGIN>>>

# 08_blue-dfir / BASICS08-02 Hop別ログソース決め打ち（Primary/Secondary・最小フィールド・相関キー・欠落時REW/CCトリガ）

## 1. 目的（本編STEPへの地続き）
本ファイルは、BASICS08-01 の「yes/no/unknown 結論テンプレ」を **運用可能にするため**、Hop1〜7ごとに
- どこから（ログソース）
- 何を（最小フィールド）
- 何で（相関キー優先順位）
を **決め打ち**し、`unknown` が出た場合は必ず **REW/CC（STEP5-14）要求**に落とす。

接続先：
- STEP5（05_pentest-chain）：DFIR章の結論（observed_by_blue）を Evidence で固定
- 06_os-network-protocol：観測点（Network/Host/Identity/Cloud）と相関キー設計
- 11_tools-lab：Run Log / Evidence Pack の作り方（採取〜紐付け〜提出）

---

## 2. 前提（Hopモデルの固定）
Hop は「攻撃チェーン上の観測単位」。本編（01〜05）で採用している Hop1〜7 に合わせ、DFIRは **Hop単位で結論**を出す。

- Hop1：入口（External / Initial Access）  
- Hop2：認証（Identity / SSO / MFA / Token）  
- Hop3：内部探索（AD/LDAP/DNS/Share Discovery）  
- Hop4：横展開（SMB/RDP/WinRM/Remote Exec）  
- Hop5：権限上昇・永続化（Role/Consent/Service Principal/Delegation 等）  
- Hop6：目的達成（データアクセス・変更・外部送信）  
- Hop7：痕跡操作・影響（ログ削除・無効化・暗号化・破壊・退避）

補助単位（重大イベント）：
- Consent付与 / Role付与 / APIキー取得 / MFA設定変更 / メール転送ルール / 監査ログ設定変更 等

---

## 3. Hop別ログソース（Primary/Secondary）決め打ち
### 3-1. 記載ルール（固定）
- Primary：**そのHopの結論を最優先で支える**ログソース（原則 1〜2個）
- Secondary：Primary を補強（相関・否定・範囲確定）するログソース
- Primary が取れない場合は `observed_by_blue: unknown` を基本とし、例外的に「設計上取れないことが確定」なら `no` に落とす

---

## 4. Hop別：ログソース・最小フィールド・相関キー（コア表）
> 表を「案件ごとに埋める」のではなく、**基準として固定**し、案件差分は注記で扱う。

### 4-1. Hop1：入口（External / Initial Access）
**Primary（推奨順）**
1) WAF / CDN / LB アクセスログ（HTTP edge）  
2) Reverse Proxy / Web Server Access Log（L7）

**Secondary**
- DNS（Resolver/Authoritative）ログ、FW/Proxy（egress/ingress）、EDR（初回実行/初回通信）、メールGW（入口がメールの場合）

**最小フィールド（必須）**
- timestamp, src_ip, http_method, url/path, status, user_agent
- (可能なら) request_id/trace_id, host, tls_sni, xff

**相関キー優先順位**
1) request_id / trace_id（edgeで採番されるなら最強）
2) x-request-id 等（アプリ引き回し）
3) 時刻±15m + src_ip + host + path

**典型unknown要因**
- edgeログ未保存、WAF未導入、アクセスログがアプリ側にしかない（相関不能）

---

### 4-2. Hop2：認証（Identity / SSO / MFA / Token）
**Primary（推奨順）**
1) IdP サインインログ（例：Entra ID / Okta / Ping / ADFS 等の認証ログ）
2) SaaS 側の認証監査ログ（アプリのサインイン監査）

**Secondary**
- Conditional Access / MFAログ、端末認証ログ（MDM/Device compliance）、VPNログ（認証経路がVPNなら）

**最小フィールド（必須）**
- timestamp, user(principal), result(success/fail), src_ip, device_id(if any), app/client_id, auth_method, mfa_detail
- correlation_id / request_id / session_id（存在する範囲で）

**相関キー優先順位**
1) correlation_id / request_id（IdPが提供）
2) session_id（SaaS側セッションID）
3) 時刻±1h + user + src_ip + app

**典型unknown要因**
- サインインログ保持切れ、抽出権限不足、MFAログが別系統で統合されていない

---

### 4-3. Hop3：内部探索（AD/LDAP/DNS/Share Discovery）
**Primary（推奨順）**
1) DC（Domain Controller）Security Log（認証・LDAP・Kerberos 関連が追える構成なら）
2) EDR / Sysmon（端末側のプロセス・ネットワーク・認証痕跡）

**Secondary**
- DNSログ（内部名前解決）、File Server監査（Share列挙）、NDR（内部east-west）

**最小フィールド（必須）**
- timestamp, host, user, process/image, commandline, dst_ip/dst_host, protocol/port
- (可能なら) eventRecordId, logon_id, kerberos_ticket_id 相当

**相関キー優先順位**
1) logon_id / logon_guid（端末・DCで共通化できるなら）
2) eventRecordId（単体証跡として固定）
3) 時刻±1h + host + user + dst（DC/LDAP/DNS）

**典型unknown要因**
- DC監査ポリシー不足、EDR未導入、Sysmon未設定、DNSログ未取得

---

### 4-4. Hop4：横展開（SMB/RDP/WinRM/Remote Exec）
**Primary（推奨順）**
1) 対象ホスト Security Log（ログオン種別/リモートログオン）
2) EDR（リモート実行ツール/横展開TTPのテレメトリ）

**Secondary**
- DC Security Log（認証イベント）、FW/NetFlow（445/3389/5985等）、RDP Gatewayログ

**最小フィールド（必須）**
- timestamp, src_host/src_ip, dst_host, user, logon_type, result, process(image), service_name(if any)
- (可能なら) logon_id, eventRecordId, network_session_id

**相関キー優先順位**
1) logon_id（同一ログオンセッションの束ね）
2) eventRecordId（証跡固定）
3) 時刻±15m + user + src_host + dst_host + port

**典型unknown要因**
- 監査未設定（ログオンが取れない）、端末ログ保持短い、NDR/NetFlowなしで通信相関不可

---

### 4-5. Hop5：権限上昇・永続化（Role/Consent/Policy/Delegation）
**Primary（推奨順）**
1) Cloud / SaaS の Audit Log（例：M365 Unified Audit Log、Azure Activity、CloudTrail 等）
2) IdP の管理操作ログ（管理者操作・ポリシー変更・アプリ登録）

**Secondary**
- CIEM/SSPM、EDR（管理用ツール実行）、Ticketing/Change管理（正当変更の否定材料）

**最小フィールド（必須）**
- timestamp, actor, operation, target(resource/object), result, admin_unit/tenant, client_ip
- (可能なら) operation_id / correlation_id

**相関キー優先順位**
1) operation_id / correlation_id（監査で採番されるもの）
2) target_object_id（対象固定）
3) 時刻±1h + actor + operation + target

**典型unknown要因**
- 監査ログ未有効化、Unified Audit Log未有効/保持不足、管理操作ログが別契約/別コンソール

---

### 4-6. Hop6：目的達成（データアクセス・変更・外部送信）
**Primary（推奨順）**
1) 対象SaaS/アプリの監査ログ（ファイルアクセス、メール操作、DB操作、管理APIアクセス）
2) DLP / CASB / Proxy（外部送信・大量DL）

**Secondary**
- Cloud storage access log、EDR（圧縮・アップロード）、NDR（大容量転送）

**最小フィールド（必須）**
- timestamp, actor/user, object(file/mail/db), action(read/download/export/delete), volume(count/bytes), src_ip/device, outcome
- (可能なら) object_id, operation_id, download_session_id

**相関キー優先順位**
1) operation_id / object_id
2) session_id（アプリ側）
3) 時刻±1h + actor + object + action

**典型unknown要因**
- 監査粒度が粗い（閲覧の証跡が出ない）、DLP/CASB未導入、プロキシバイパス経路

---

### 4-7. Hop7：痕跡操作・影響（ログ削除・無効化・暗号化等）
**Primary（推奨順）**
1) 監査ログ設定変更の監査（Audit Log：logging/retention/forwarding の変更）
2) EDR（防御回避、ログ削除、暗号化挙動、停止操作）

**Secondary**
- SIEM 側の取り込み停止/遅延のメトリクス、バックアップログ、DR/BCPログ

**最小フィールド（必須）**
- timestamp, actor, change(operation), target(config), before/after(if any), result, src_ip
- (EDR) process/image, commandline, affected_files/count

**相関キー優先順位**
1) operation_id / change_id
2) eventRecordId（端末）
3) 時刻±1h + actor + target + change

**典型unknown要因**
- 監査停止が先に起きてログ自体が残らない（「観測不能」になりやすい）

---

## 5. “最小フィールド” 共通スキーマ（Evidence Pack の固定）
BASICS08-01 の evidence_id（E-###）に紐づく「ログの最小提出形」を固定する。

### 5-1. 共通フィールド（全Hop共通）
- timestamp（ISO, JST優先）
- actor（user/service principal/role/device のいずれか）
- source（src_ip / src_host / device_id）
- target（resource/app/host/object_id）
- action（operation/event_name）
- outcome（success/fail + reason）
- correlation_key（後述のキーを必ず1つ以上）

### 5-2. Evidence Pack のファイル粒度（推奨）
- E-###_raw.(csv|json|evtx|gz) ：生ログ（抽出物）
- E-###_normalized.csv ：最小フィールドに正規化
- E-###_note.md ：このEvidenceが示す主張（1〜3行）と抽出条件

---

## 6. 相関キー（優先順位の固定）と“最後の手段”
### 6-1. 相関キー優先順位（固定）
1) request_id / trace_id / correlation_id / operation_id（**横断相関の第一候補**）
2) session_id / logon_id（**同一セッション束ね**）
3) object_id / target_id（**対象固定**）
4) eventRecordId（**単点証拠の固定**）
5) 時刻±Δ + actor + source + target（**最後の手段**）

### 6-2. “最後の手段” の許容条件
- DFIR結論は yes/no/unknown のため、相関が弱い場合は confidence を下げる（詳細版のみ）
- 時刻相関のみで yes に倒さない（原則：unknown か、補強Evidenceが揃ってから yes）

---

## 7. 欠落時の REW/CC（STEP5-14）トリガ（Hop別）
> `unknown` が出たら、必ず「何がないか」を **要求に変換**する（放置禁止）。

### 7-1. トリガ定義（固定）
- Trigger-A：Primary ログが存在しない / 無効  
- Trigger-B：保持期限切れ（過去期間が取れない）  
- Trigger-C：抽出権限がない / 抽出担当が不明  
- Trigger-D：相関キー欠落（結論が固定できない）  
- Trigger-E：SIEM未連携（横断相関ができない）

### 7-2. Hop別要求テンプレ（コピペ用）
~~~~
REW/CC Request (HopN):
- trigger: Trigger-A|B|C|D|E
- requested_log_source: (Primary/Secondaryの具体名)
- requested_time_window: YYYY-MM-DDTHH:MM+09:00 ~ YYYY-MM-DDTHH:MM+09:00
- minimum_fields_needed:
  - timestamp
  - actor
  - source
  - target
  - action
  - outcome
  - correlation_id|request_id|session_id|eventRecordId
- reason:
  - (observed_by_blue を yes/no/unknown のどれに確定するために必要か)
- delivery_format:
  - csv|json|evtx|portal_export|api_export
- owner:
  - client|tester|both
~~~~

### 7-3. Hop別 “最頻要求” の決め打ち（例）
- Hop1：WAF/CDNの request_id 付きアクセスログ（またはLBログ）  
- Hop2：IdPサインインログ＋correlation_id（MFA詳細含む）  
- Hop3：DC監査（Kerberos/LDAP）＋端末EDR（プロセス/通信）  
- Hop4：端末Securityログ（ログオン種別）＋EDR（remote exec）  
- Hop5：監査ログ（Role/Consent/Policy変更）＋operation_id  
- Hop6：対象SaaS監査（DL/Export）＋DLP/Proxy（外部送信）  
- Hop7：監査設定変更ログ＋EDR（ログ削除/停止/暗号化）

---

## 8. 11_tools-lab / 06 との具体接続（運用の固定点）
- 11_tools-lab/（Run Log）に **correlation_key を必須**にする  
  - Hopごとの evidence_id（E-###）と correlation_key を 1:1 で結び、BASICS08-01 の evidence_basis を埋められる状態にする
- 06_os-network-protocol（観測点）  
  - Network（WAF/Proxy/DNS/FW）× Host（EDR/OS）× Identity（IdP）× Cloud（Audit）を Hopに割り当て、**観測点の穴=unknown** を早期に露出させる

---

## 9. すぐ使える “Hop別ログ採取チェックリスト”（短縮版）
### 9-1. Engagement開始時（Day0）に必ず確認
- IdPサインインログ：保持期間 / 抽出手段 / 権限
- Cloud/SaaS監査ログ：有効化状況 / Unified Audit Log 等のON/OFF
- EDR：導入範囲（どの端末/サーバ）/ テレメトリ保持
- DC/端末ログ：保持と監査ポリシー（Securityログが残るか）
- SIEM：取り込み有無（横断相関できるか）

### 9-2. unknown を減らす最小アクション（優先）
1) Hop2（IdP）と Hop5（監査）の Primary を確保  
2) Hop4（横展開）に必要な Host/EDR を確保  
3) Hop1（入口）の request_id 系を確保（アプリ相関に効く）

---

## 10. 次のファイル（BASICS08-03）への接続
次は、BASICS08-02 で決め打ちしたログソースを **“抽出クエリ・抽出手順・正規化”** まで落とし、Evidence Pack を量産可能にする。

- 次：keda-lab/08_blue-dfir/03_evidence-pack-playbook.md
  - Hop別：抽出手順（portal/api/siem）
  - 正規化カラム（common schema）
  - Evidence ID（E-###）採番ルール
  - 提出物チェック（欠落時のREW/CC自動トリガ条件）

<<<END>>>