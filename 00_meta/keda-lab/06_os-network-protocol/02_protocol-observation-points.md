<<<BEGIN>>>
# 06_os-network-protocol / BASICS06-02 プロトコル別 観測点定義（最小フィールド＋Evidence種別への落とし込み）

## 1. 目的（本編STEPへの地続き）
本ファイルは、プロトコルごとに「何を見れば Evidence として成立するか」を固定し、以下へ直結させる。

- STEP3（AD/横展開）：AD-DISC / LM-LOG の根拠を“観測点”で確定する
- STEP5（Report/DFIR）：Timeline と DFIR 章を“相関キー”で成立させる
- 11_tools-lab：BASICS11-06 の最小実行セットが “何を取るべきか” 迷わないようにする

---

## 2. 記号・前提
- Evidence 種類（短縮）：OSINT / AUTH-LOG / CA-POL / AD-DISC / LM-LOG / PRIV / AADC / CLOUD-AUDIT / API-SAMPLE / DFIR / ROE / HTTP-EVID
- 観測ソース分類：
  - `client`：テスト端末側（実行ログ/パケット/アプリログ）
  - `server`：対象サーバ側（OS/アプリログ）
  - `control-plane`：管理プレーン（IdP/Entra/EDR/SIEM 等）
  - `network`：NW機器/センサー（FW/Proxy/DNS等）
- 基準時計：BASICS11-04 の Primary Clock に従う

---

## 3. プロトコル別：観測点（最小）

## 3-1. DNS（入口/到達の根拠：STEP1/3）
### 目的
- 入口候補（FQDN）の実在、解決先、内部/外部の境界を裏付ける

### 最小観測点（Must）
- `query_name`：問い合わせたFQDN
- `answer`：A/AAAA/CNAME の結果
- `time_iso_jst`：問い合わせ時刻
- `resolver`：問い合わせ先（OS/社内DNS/パブリック）
- `source`：どの入口候補から導いたか（OSINT参照）

### Evidence への落とし込み
- OSINT：入口候補＋DNS根拠（sanitized）
- Timeline：入口選定の理由（Hop1）

### 相関キー
- 強い相関キーは無い → 時刻と対象名を厳密に残す

---

## 3-2. HTTP（Web/SSO/管理画面：STEP1/2/5）
### 目的
- 入口の事実、認証フローの遷移、Cookie/Token 受け渡しの “構造” を示す（機微はマスク）

### 最小観測点（Must）
- `method` / `url`（パスまで、クエリは必要部分のみ）
- `status_code`
- `response_headers_subset`（Location、Set-Cookie の“存在”）
- `time_iso_jst`
- `request_id/trace_id`（あれば）
- `auth_flow_step`（例：redirect→callback→session）

### Evidence への落とし込み
- HTTP-EVID：リク/レス（sanitized）＋再現手順＋影響根拠（シナリオBで必須）
- AUTH-LOG/CA-POL：HTTPフローだけで断定せず、control-plane ログで補強（L1→L2）

### 相関キー
- `trace_id` / `request_id` / `correlation_id`（アプリ/プロキシ/クラウド）
- 無い場合：時刻±1h、ユーザー、IP、アプリで突合

---

## 3-3. TLS（証明書・経路・中間者：STEP1/2）
### 目的
- 入口の真正性、証明書情報、TLS終端（Proxy/IdP）を観測し、認証基盤の前提誤読を防ぐ

### 最小観測点（Must）
- `sni`（接続先ホスト）
- `cert_subject` / `issuer`（要約）
- `not_before/not_after`（有効期限）
- `alpn`（http/1.1, h2 等、あれば）
- `time_iso_jst`

### Evidence への落とし込み
- OSINT/HTTP-EVID の補助根拠（入口・経路の理解）
- 認証基盤（STEP2）の説明根拠（終端の推定）

---

## 3-4. LDAP（ディレクトリ探索：STEP3）
### 目的
- AD/LDAP の構造と探索結果（AD-DISC）を“検索の事実”として残す

### 最小観測点（Must）
- `server`（LDAP/GC の宛先）
- `bind_type`（匿名/認証あり：機微はマスク）
- `base_dn`（sanitized）
- `scope`（base/one/sub）
- `filter`（sanitized：個人特定につながる値は伏せる）
- `result_count`（件数）
- `time_iso_jst`

### Evidence への落とし込み
- AD-DISC：要約表（OU/Group/重要候補）＋ “どの検索で得たか” のメタ
- Timeline：Hop3（構造把握→宛先選定）の1行叙述

### 相関キー
- 取れるなら：サーバログの request/correlation
- 無い場合：時刻＋宛先サーバ＋検索種別（base_dn/scope）で整合

---

## 3-5. Kerberos（チケット/認証：STEP3）
### 目的
- “認証要求が起きた/チケットが発行された” を事実化し、解釈ミス（L2誤主張）を防ぐ

### 最小観測点（Must）
- `source_host`（要求元）
- `account`（sanitized）
- `service_principal`（sanitized）
- `result`（success/fail）
- `time_iso_jst`
- `dc`（対象DC）

### Evidence への落とし込み
- AD-DISC（補助）：Kerberos周りの兆候（委任/サービス）を説明する根拠
- DFIR（補助）：DCログが取れるなら“検知/追跡可能性”を示す

### 相関キー
- DC側イベントの `RecordId` 等（取れる範囲）
- 無い場合：時刻＋アカウント＋サービスで突合

---

## 3-6. SMB（共有/セッション：STEP3/5）
### 目的
- 共有列挙・セッション成立を根拠化し、横展開の “到達” を LM-LOG で示す

### 最小観測点（Must）
- `target_host`
- `auth_result`（success/fail）
- `account`（sanitized）
- `share_action`（list/access の区別）
- `time_iso_jst`

### Evidence への落とし込み
- LM-LOG：サーバ側ログオン事実（該当時刻±15分）＋実行ログ
- AD-DISC：共有/サーバ候補の選定理由（過収集せず要約）

### 相関キー
- Windows ログの `eventRecordId` / EDR session id（あれば）
- 無い場合：時刻±15分＋対象ホスト＋アカウントで突合

---

## 3-7. RDP（対話型リモート：STEP5）
### 目的
- “対話アクセスが成立した” の事実（到達）を LM-LOG として示す

### 最小観測点（Must）
- `target_host`
- `logon_type`（対話/リモート）
- `account`（sanitized）
- `source_ip/host`（sanitized）
- `result`
- `time_iso_jst`

### Evidence への落とし込み
- LM-LOG：ログオン事実＋対象・時刻
- DFIR：検知/未検知の根拠（EDR/Windows/監視）

### 相関キー
- `eventRecordId`（可能なら）
- 無い場合：時刻±15分＋対象＋アカウント

---

## 3-8. WinRM（管理API/リモート実行：STEP5）
### 目的
- “管理操作が成立した” の事実を残し、L2/L3 の主張根拠を強くする

### 最小観測点（Must）
- `target_host`
- `account`（sanitized）
- `operation`（command/ps/remoting）
- `result`
- `time_iso_jst`

### Evidence への落とし込み
- LM-LOG：操作成立の根拠（ログ/EDR）
- PRIV：必要権限の根拠（ローカル管理者等）

---

## 4. “パケット vs ログ”の最小判断（Evidenceとしてどちらを優先するか）
- 原則：
  - **ログ（control-plane / server）** が取れるならログ優先（監査性が高い）
  - パケットは補助（根拠強化、ログ欠落時のフォールバック）
- 例外：
  - Web の HTTP-EVID は、リク/レス（sanitized）が主根拠になり得る（ただし認証/認可はログで補強）

---

## 5. よくある誤読（次ファイルで詳細化する前の要点）
- “接続できた”＝“権限がある”ではない（HTTP/SMB/RDPで頻発）
- “リダイレクトした”＝“認証できた”ではない（SSO/HTTPで頻発）
- “列挙できた”＝“過権限”ではない（AD/LDAPで頻発）
- “監査ログがある”＝“検知できる”ではない（DFIRで頻発）

---

## 6. 次のファイル（BASICS06-03）への接続
次は、プロトコル横断で「相関キー」「イベント種類」「Evidence種別」の対応をチートシート化する。

- 次：keda-lab/06_os-network-protocol/03_log-and-correlation-cheatsheet.md
  - Hop別：最低限見るログ
  - 相関キー：何を拾えば突合できるか
  - Evidence Index/Timeline へ落とすための“固定フィールド”
  - DFIR章にそのまま貼れる形

<<<END>>>
