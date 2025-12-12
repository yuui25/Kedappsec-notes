
<<<BEGIN>>>
# 06_os-network-protocol / BASICS06-03 ログ＆相関キー チートシート（Hop別：最低限見るログ→Evidence Index/Timelineへ直結）

## 1. 目的（本編STEPへの地続き）
本ファイルは、各Hopで「最低限どのログを見て」「何の相関キーで突合し」「Evidenceとして何を残すか」を固定し、以下へ直結させる。

- STEP3（AD/横展開）：AD-DISC / LM-LOG を“根拠ログ”で成立させる
- STEP4（Cloud）：CLOUD-AUDIT / API-SAMPLE の突合（request_id等）を成立させる
- STEP5（Evidence/DFIR）：Timeline の因果と DFIR 章の“検知可否”を機械的に書けるようにする
- 11_tools-lab：Run Log（BASICS11-02）に残すべき相関キーを決め打ちにする

---

## 2. チートシートの使い方（固定）
1) Hop を特定（Hop1〜7）  
2) 「最低限見るログ（Primary）」を押さえる  
3) 取れる場合は「補助ログ（Secondary）」も押さえる  
4) 相関キー（Correlation Keys）を Run Log に必ず残す  
5) Evidence Index に “ログ抜粋” を登録し、Timeline に “1行叙述” を登録する  
6) DFIR 章は、検知/未検知/不明（ログ欠落）のいずれかで必ず結論を書く（unknownも可）

---

## 3. Hop別：最低限ログ＆相関キー

## Hop1（Recon：入口・露出）
### Primary（最低限）
- OSINT根拠（URL/スクショ/参照元）
- DNS解決結果（必要なら）
- HTTP疎通（入口の事実）

### Correlation Keys（相関キー）
- 基本は弱い → `time_iso_jst` と `target（FQDN/URL）` を厳密に残す

### Evidence（登録）
- OSINT（入口一覧＋根拠）
- HTTP-EVID（入口のHTTP応答要約、必要なら）

### Timeline event_type（推奨）
- `recon_discovery`

---

## Hop2（Session：認証・制御）
### Primary
- AUTH-LOG（IdP/VPN/VDI のサインインログ）
- CA-POL（条件付きアクセス/例外の現在値）

### Secondary（あると強い）
- Proxy/FWの認証系ログ（SSOの前段）
- アプリ側のアクセスログ（request_idがあるなら）

### Correlation Keys
- `user`（sanitized）
- `app`（対象アプリ）
- `ip`（必要ならマスク）
- `request_id / correlation_id / session_id`（存在する場合は最優先）
- `time_window`（±1h）

### Evidence
- AUTH-LOG（抜粋）
- CA-POL（現在値）
- DFIR（検知/追跡可能性：ログ有無の根拠）

### Timeline event_type
- `signin_attempt` / `policy_evaluated`

---

## Hop3（AD Discovery：構造・宛先選定）
### Primary
- AD-DISC（要約表＋根拠）
- LDAP/Kerberos/SMB関連の“成立ログ”（取れる範囲）

### Secondary
- EDRの探索/列挙イベント（あれば）
- DC/サーバ側の監査ログ（可能なら）

### Correlation Keys
- `dc/server`（対象）
- `account`（sanitized）
- `eventRecordId`（Windows系が取れる場合）
- `time_iso_jst`
- LDAPなら：`base_dn/scope/filter(hash化でも可)`（sanitized）

### Evidence
- AD-DISC（要約＋根拠）
- DFIR（列挙が観測可能か：可能なら）

### Timeline event_type
- `directory_discovery`

---

## Hop4（Material：材料＝権限根拠）
### Primary
- PRIV（Role/Group/Consent の根拠）
- 可能なら CLOUD-AUDIT（権限付与/変更の監査ログ）

### Secondary
- チケット/セッション更新（Auth周り）ログ

### Correlation Keys
- `subject`（実行者）
- `object`（割当対象）
- `operation`（role_assign/consent/grant 等）
- `correlation_id`（クラウド監査ログで取れるなら）

### Evidence
- PRIV（根拠）
- CLOUD-AUDIT（変更があった事実）

### Timeline event_type
- `privilege_observed` / `permission_changed`

---

## Hop5（Lateral：到達・横展開）
### Primary
- LM-LOG（ログオン/接続成立の根拠：対象時刻±15分）
- PRIV（必要権限の根拠）
- DFIR（検知/未検知/不明の結論）

### Secondary
- EDR telemetry（process、network、remote session）
- FW/Proxyの通信ログ（横展開経路が必要な場合）

### Correlation Keys
- `target_host`
- `source_host/ip`（sanitized）
- `account`（sanitized）
- `logon_type`（RDP/SMBの区別に有効）
- `eventRecordId`（取れるなら最優先）
- `edr_alert_id/session_id`（あれば）

### Evidence
- LM-LOG（抜粋＋実行ログ）
- DFIR（アラート有無の根拠）
- PRIV（根拠）

### Timeline event_type
- `lateral_move` / `remote_logon`

---

## Hop6（Hybrid：同期点・波及）
### Primary
- AADC（同期点の構成証跡）
- CLOUD-AUDIT（関連する管理操作の監査ログ）

### Secondary
- オンプレ側のサービスログ（同期関連）
- SIEM転送状態（抽出可否）

### Correlation Keys
- `service/component`（AADC等）
- `operation`（config_change/sync/role_change）
- `correlation_id`（クラウド側が出すなら）
- `time_window`（±1h）

### Evidence
- AADC（構成証跡）
- CLOUD-AUDIT（操作ログ）
- DFIR（検知可否：ログ有無）

### Timeline event_type
- `hybrid_pivot` / `sync_point_observed`

---

## Hop7（Cloud：監査ログ＋API最小サンプル）
### Primary
- CLOUD-AUDIT（Entra Audit/Sign-in）
- API-SAMPLE（メタ情報のみの最小サンプル）
- PRIV（Role/Consent 根拠）

### Secondary
- SaaS側の監査ログ（SharePoint等、可能なら）
- Proxy/Cloud App Security系ログ（あれば）

### Correlation Keys
- `correlation_id / request_id`（最優先）
- `user`（sanitized）
- `app/resource`（対象リソース）
- `operation`（consent_grant/role_assign/api_call 等）
- `time_window`（±1h）

### Evidence
- CLOUD-AUDIT（抜粋）
- API-SAMPLE（HTTP 200＋取得項目一覧：本文無し）
- PRIV（根拠）
- DFIR（検知/追跡可否）

### Timeline event_type
- `cloud_admin_action` / `api_access`

---

## 4. Evidence Index / Timeline に落とすための固定フィールド（最低限）
### 4-1. Evidence Index（最低限）
- `evidence_id`
- `hop`
- `scenario`
- `title`
- `claim_level`
- `collected_at_iso_jst`
- `source_type`（onprem/cloud/saas/osint）
- `source_name`
- `actor`
- `artifact_type`
- `path_raw` / `path_sanitized`
- `sha256_raw` / `sha256_sanitized`
- `confidentiality`
- `pii_flag` / `secrets_flag`
- `correlation_key`（文字列でOK）

### 4-2. Timeline（最低限）
- `event_id`
- `time_iso_jst`
- `scenario`
- `hop`
- `event_type`
- `actor`
- `target`
- `result`
- `claim_level`
- `evidence_ids`
- `narrative_1line`

---

## 5. DFIR章へそのまま貼れる“結論テンプレ”（検知評価の最小）
DFIR は “unknown” を許容しつつ、必ず結論を書く。

~~~~
DFIR-Conclusion (HopN):
- observed_by_blue: yes|no|unknown
- evidence_basis: (E-###;E-###) or (log_not_available)
- what_blue_can_see: (最小で：誰が/どこで/何をした が追えるか)
- gaps: (不足ログ、相関キー欠落、保持不足)
- recommendation: (ログ取得/保持/相関キー/アラート改善)
~~~~

---

## 6. 次のファイル（BASICS06-04）への接続
次は「パケット（通信）とログ（監査）のどちらをEvidence主根拠にするか」を、ケース別に最小で決め打ちする。

- 次：keda-lab/06_os-network-protocol/04_minimal-packet-vs-log-evidence.md
  - 代表ケース（HTTP/SSO、SMB/RDP、LDAP/Kerberos、Cloud API）
  - 主根拠＝ログ、補助＝パケット の原則と例外
  - ログ欠落時のフォールバック（STEP5-11）接続

<<<END>>>
