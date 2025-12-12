フォルダ名: keda-lab/06_os-network-protocol/
ファイル名: 01_overview.md
STEP名: BASICS06-01 OS/Network/Protocol 概観（観測点＝Evidenceの根拠を固定しSTEP3/5へ接続）

<<<BEGIN>>>
# 06_os-network-protocol / BASICS06-01 OS/Network/Protocol 概観（観測点＝Evidenceの根拠を固定しSTEP3/5へ接続）

## 1. 目的（このフォルダが補う本編STEP）
06_os-network-protocol は「プロトコル解説」ではなく、**攻撃・防御の“事実（Fact）”を作る観測点の辞書**である。

- 補強対象
  - STEP1（01_asm-recon）：Recon の仮説を “通信・到達” で裏付ける
  - STEP3（03_ad-kerberos-lateral）：SMB/RDP/LDAP/Kerberos の列挙・横展開を“ログ/相関キー”で根拠化する
  - STEP4（04_cloud）：オンプレ→クラウドの接続（Proxy/IdP/同期）の観測点を整理する
  - STEP5（05_pentest-chain）：Evidence Index / Timeline / DFIR 章に落とすための「何を見るべきか」を固定する

---

## 2. 本フォルダの成果物（何が手に入るか）
- プロトコル別：
  - “何が起きたか” を証明する最小ログ/フィールド
  - 失敗/成功の判定条件（最小）
  - 相関キー（request_id、eventRecordId、session_id など）
- 本編への接続：
  - Hop（Hop1〜7）ごとの “観測点” と Evidence 種類（LM-LOG/AD-DISC/DFIR 等）対応

---

## 3. 観測の基本モデル（固定）
### 3-1. 3レイヤ観測（最小）
- L7（アプリ/プロトコル）：HTTP、LDAP、Kerberos などのイベント
- L4（コネクション）：接続成否、セッション、ポート、フロー
- L2/L3（ネットワーク）：ARP/DNS/ルーティング、到達可能性

※Evidence として強いのは、原則として L7 の事実＋相関キー。

### 3-2. “成功” の定義（統一）
- “コネクションが張れた” だけでは成功扱いしない（誤主張防止）
- 目的に応じて成功を定義する：
  - 認証成功（サインイン）
  - 認可成立（権限でデータが見える）
  - 実行成立（操作が反映される）
- Run Log（BASICS11-02）の `success_criteria` に落とす

---

## 4. プロトコル別：本編で頻出する観測点（概観）

### 4-1. DNS（STEP1/3の基礎）
- 観測目的：
  - 入口/内部名の解決、到達先選定の根拠
- Evidence 化しやすいもの：
  - 解決結果（A/AAAA/CNAME）、問い合わせ時刻、サーバ
- 相関キー：
  - 直接の相関キーは弱いので、時刻と対象名を厳密に残す

### 4-2. HTTP/TLS（STEP1/2/5：Web/SSO/管理画面）
- 観測目的：
  - 入口の事実、認証フロー、リダイレクト、Cookie/Tokenの受け渡し（ただし機微はマスク）
- Evidence：
  - HTTP リク/レス（sanitized）、TLS 証明書情報（必要なら）
- 相関キー：
  - trace_id/request_id、サーバログの correlation id

### 4-3. LDAP（STEP3：ディレクトリ探索）
- 観測目的：
  - ディレクトリ構造の取得、委任/権限の兆候
- Evidence：
  - “どの検索で何が得られたか” の要約（AD-DISC）
- 相関キー：
  - サーバ側ログが取れれば強いが、取れない場合は実行時刻＋対象DC/LDAPサーバ

### 4-4. Kerberos（STEP3：認証とチケット）
- 観測目的：
  - 認証要求/チケット発行の事実、攻撃/誤読の回避
- Evidence：
  - DC側ログ（可能なら）＋実行ログ
- 相関キー：
  - イベントID/RecordId、要求元、アカウント

### 4-5. SMB（STEP3/5：横展開・ファイルアクセス）
- 観測目的：
  - セッション成立、共有列挙、認証成否
- Evidence：
  - サーバ側のログオン事実（LM-LOG）＋アクセス痕跡
- 相関キー：
  - ログオンイベント、セッション情報

### 4-6. RDP/WinRM（STEP5：横展開の到達証明）
- 観測目的：
  - リモート対話/管理操作の成立（到達の証明）
- Evidence：
  - ログオン種別、成功/失敗、接続元、対象
- 相関キー：
  - eventRecordId、EDRのセッションID

---

## 5. Hop（本編STEP5）への接続（概観）

### Hop1（Recon）
- DNS/HTTP の観測で“入口の事実”を固め、OSINT Evidence として残す

### Hop2（Session）
- HTTP/TLS（SSOフロー）＋ AUTH-LOG/CA-POL の観測で “制御が効いた/効かない” を事実化

### Hop3（AD Discovery）
- LDAP/Kerberos/SMB の観測で “列挙結果の根拠” を AD-DISC として残す

### Hop5（Lateral）
- SMB/RDP/WinRM の観測で “到達と権限” を LM-LOG/PRIV として残す
- Blue 側ログ（DFIR）と突合するため相関キーを意識する

### Hop7（Cloud）
- HTTP/API の観測で “APIアクセスの成立（L2）” を API-SAMPLE として残す
- 監査ログ（CLOUD-AUDIT）と request_id 等で突合する

---

## 6. このフォルダの標準ファイル（今後作るもの）
~~~~
keda-lab/06_os-network-protocol/
  01_overview.md
  02_protocol-observation-points.md
  03_log-and-correlation-cheatsheet.md
  04_minimal-packet-vs-log-evidence.md
  05_common-misreads-and-how-to-avoid.md
~~~~

---

## 7. 次のファイル（BASICS06-02）への接続
次は、プロトコル別に「何を見れば Evidence として成立するか」を実務用に固定する。

- 次：keda-lab/06_os-network-protocol/02_protocol-observation-points.md
  - DNS / HTTP/TLS / LDAP / Kerberos / SMB / RDP/WinRM
  - 目的別（列挙/認証/横展開/クラウドAPI）に、最小観測点と最小フィールドを定義
  - Evidence 種類（OSINT/AD-DISC/LM-LOG/DFIR/API-SAMPLE）への落とし込み

<<<END>>>
