フォルダ名: keda-lab/06_os-network-protocol/
ファイル名: 04_minimal-packet-vs-log-evidence.md
STEP名: BASICS06-04 ログ優先・パケット補助のEvidence設計（ケース別：主根拠/例外/フォールバック）

<<<BEGIN>>>
# 06_os-network-protocol / BASICS06-04 ログ優先・パケット補助のEvidence設計（ケース別：主根拠/例外/フォールバック）

## 1. 目的（本編STEPへの地続き）
本ファイルは、Evidenceとして「ログ」と「パケット（通信）」のどちらを主根拠にするかをケース別に固定し、以下を成立させる。

- STEP3（AD/横展開）：列挙/到達の主張を“監査性の高い根拠”で支える
- STEP5（Evidence/DFIR）：Timeline の因果と DFIR の検知評価を“相関キー中心”で破綻させない
- 11_tools-lab：過収集（FM-02）を避けつつ、欠落時のフォールバック（STEP5-11）を迷わず選べる

---

## 2. 原則（固定）
### 2-1. 原則：主根拠＝ログ（server/control-plane）、パケット＝補助
- ログは監査性が高く、DFIR・再現・説明に強い
- パケットは強力だが、以下のリスクがある
  - 過収集（PII/秘密混入）
  - 暗号化で見えない（TLS）
  - 保存・管理が重い（サイズ/取り扱い）

### 2-2. 例外：WebのHTTP-EVIDは“sanitizedリク/レス”が主根拠になり得る
ただし「認証/認可の成立」は、可能な限り control-plane のログで補強し、L2主張を安定させる。

### 2-3. ログ欠落時は“フォールバック階層”で必ず結論を出す
- ログが取れない＝unknown を許容  
- ただし、unknown の理由（権限/保持/設計）と改善提案を必ず残す（DFIRテンプレに落とす）

---

## 3. 判断フレーム（迷わないためのチェック）
ケースごとに次の順で判断する。

1) **主張したいものは何か？**（接続/認証/認可/実行/権限変化）
2) **主張レベルは？**（L1/L2/L3）
3) **最短で監査性が高い根拠はどこにあるか？**  
   - control-plane（IdP/Entra/EDR/SIEM）→ server → client → packet の順
4) **相関キーが取れるか？**（request_id/eventRecordId/session_id）
5) **取れないならフォールバックを選ぶ**（unknown含む）

---

## 4. ケース別：主根拠/補助/フォールバック

## 4-1. Case A：HTTP/SSO（Web入口〜サインイン）
### 主張の典型
- リダイレクトフローが成立した（L1）
- サインインが成功し、CA/MFAが評価された（L1〜L2）
- セッションで到達範囲が変わった（L2）

### 主根拠（優先順）
1) control-plane：AUTH-LOG / CA-POL（サインイン結果、MFA要求、CA評価）
2) server：アプリ/Proxyログ（request_id/trace_id）
3) client：HTTP履歴（sanitized：Location/Set-Cookieの“存在”）

### パケット（補助として有効な場面）
- Proxy/アプリログが取れず、HTTPの事実（status/redirect）を示したい
- ただしTLSで見えない可能性が高い → “HTTPキャプチャ（プロキシログ）”のほうが現実的

### フォールバック（ログ欠落時）
- AUTH-LOGが取れない：HTTP-EVIDでL1（観測）に落とし、L2主張はしない
- CA評価が不明：unknown とし、ログ保持/権限を REW で要求（STEP5-14）

---

## 4-2. Case B：LDAP（ディレクトリ探索）
### 主張の典型
- ある検索で、ある種の情報が得られた（L1〜L2）
- 列挙結果を根拠に高価値候補を選定した（L1）

### 主根拠
1) server：LDAP/AD側ログ（取れるなら最強）
2) client：実行出力（raw）＋検索メタ（base_dn/scope/filterのsanitized）
3) control-plane：EDRが探索イベントを持つなら補強

### パケット（補助として有効な場面）
- サーバログが取れない場合に、検索が実行された事実を補助する
- ただし、過収集になりやすい → 必要最小（フィルタ/件数/時刻）のみ

### フォールバック
- サーバログ無し：client出力＋時刻＋対象サーバでL1主張に留める
- 個人識別子を含む列挙は避け、要約（AD-DISC）で残す

---

## 4-3. Case C：Kerberos（認証要求/チケット）
### 主張の典型
- 認証要求が発生し、成功/失敗した（L1）
- あるサービスに対する要求が観測された（L1〜L2）

### 主根拠
1) server：DCログ（イベント/RecordIdが取れるなら）
2) control-plane：EDR/監視（認証試行の痕跡があるなら）
3) client：実行ログ（時刻/対象/結果）

### パケット（補助として有効な場面）
- DCログが取れず、要求自体の発生を示したい場合
- ただし解析コストが高い → “相関キー不足”を補う目的に限定

### フォールバック
- DCログが無い：unknown を許容し、L2/L3の主張は避ける
- DFIR章で「追跡不能の理由（ログ未取得/保持不足）」を明記

---

## 4-4. Case D：SMB（共有/セッション）と横展開の入口
### 主張の典型
- セッションが成立した/しない（L1）
- 共有列挙ができた（L1）
- その後の到達（Hop5）に繋がった（L2）

### 主根拠
1) server：Windowsログオン（LM-LOG：対象時刻±15分）
2) control-plane：EDRセッション/アラート
3) client：ツール出力（成功/失敗、対象、時刻）

### パケット（補助として有効な場面）
- サーバログが取れない場合に接続試行の事実を補助
- ただし “成功” の断定には使わない（接続＝権限ではない）

### フォールバック
- サーバログ無し：client出力でL1に留め、DFIRはunknown
- 必須Evidence不足としてREW起票（ログ提供/保持延長の依頼）

---

## 4-5. Case E：RDP/WinRM（横展開の成立）
### 主張の典型
- リモートログオン/管理操作が成立した（L2）
- その操作が検知できた/できない（DFIR）

### 主根拠
1) server：ログオン/操作ログ（LM-LOG）
2) control-plane：EDR telemetry（session/process/network）
3) client：実行ログ（最小）

### パケット（補助として有効な場面）
- 通信経路（FW/Proxy）を示したい場合の補助
- ただし、成立の根拠はログ（LM-LOG）を優先する

### フォールバック
- ログが取れない：L2主張を避け、到達可能性（L1）に落とす
- DFIRはunknown、改善提案（ログ/EDR設定）を提示

---

## 4-6. Case F：Cloud API（Graph/SaaS API：Hop7のL2実証）
### 主張の典型
- APIアクセスが成立し、最小サンプルを取得できた（L2）
- 監査ログと突合できる（DFIR/監査性）

### 主根拠
1) control-plane：CLOUD-AUDIT（Audit/Sign-in）
2) client：API-SAMPLE（HTTP 200＋取得項目一覧、本文なし）
3) server（SaaS側）：監査ログ（取れるなら補強）

### パケット（補助として有効な場面）
- 原則不要。APIクライアントの出力＋監査ログで足りる
- パケットは過収集/機微混入のリスクが高い

### フォールバック
- 監査ログが取れない：API-SAMPLEはL2として成立しにくい  
  → L1（観測）に落とし、監査ログ抽出をREWで要求
- request_id/correlation_idが無い：時刻±1h＋主体＋アプリで突合し、確度を下げる

---

## 5. “フォールバック階層”の標準（ログが無い時にどうするか）
### FB-1（最優先）：control-plane ログ（IdP/Entra/EDR/SIEM）
- 取れないなら、権限・保持・抽出経路の問題として REW

### FB-2：server ログ（Windows/アプリ）
- 取れないなら、運用上の制約として unknown を許容

### FB-3：client 出力（実行ログ/結果）
- L1（観測）に落とすための最低限の根拠

### FB-4：packet（必要最小）
- “実行した事実”の補助に限定  
- 解析・保存は最小、sanitized の Fact 化ができる範囲のみ

---

## 6. 11_tools-lab への接続（運用上の注意）
- パケット取得は FM-02（過収集）と FM-07（秘密混入）を誘発しやすい  
  → `11_tools-lab/05_failure-modes-and-guardrails.md` のガードレールを必ず通す
- どのケースでも Run Log（11/02）の `purpose/success_criteria/correlation_key` を先に埋める
- Evidence化は raw/sanitized 分離＋hash-manifest（11/04）を前提にする

---

## 7. 次のファイル（BASICS06-05）への接続
次は、誤読で主張が崩れる典型をまとめ、L1/L2/L3の誤主張を防ぐ。

- 次：keda-lab/06_os-network-protocol/05_common-misreads-and-how-to-avoid.md
  - 接続＝成功、redirect＝認証成功、列挙＝過権限、監査ログ＝検知可能、など
  - 誤読を防ぐチェック（成功条件・二重根拠・相関キー）
  - 失敗パターン（11/05）とDFIRテンプレ（06/03）への接続

<<<END>>>
