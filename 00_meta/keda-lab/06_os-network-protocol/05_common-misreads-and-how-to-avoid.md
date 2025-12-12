
<<<BEGIN>>>
# 06_os-network-protocol / BASICS06-05 典型誤読と回避策（L1/L2/L3の誤主張を防ぎSTEP3/5を壊さない）

## 1. 目的（本編STEPへの地続き）
本ファイルは、プロトコル/ログの誤読により「主張が崩れる」「Evidenceが弱い」「DFIRが書けない」事故を防ぐ。

- STEP2（Auth）：SSO/MFA/CA の誤読（redirect＝成功 等）を防ぐ
- STEP3（AD/横展開）：列挙/接続/認証の誤主張を防ぐ
- STEP5（Report/DFIR）：L2/L3を誤って主張せず、二重根拠で成立させる
- 11_tools-lab：FM-04（誤検知・誤読）を具体的に潰す

---

## 2. 誤読防止の共通ルール（固定）
### R-01 成功条件（Success Criteria）を“先に”定義する
- Run Log（11/02）の `success_criteria` を空欄のまま実行しない
- 成功条件は「何が出ればOKか」をログ/フィールドで書く

### R-02 L1/L2/L3 を混ぜない
- L1：観測（設定/ログ/画面）  
- L2：最小サンプルで成立（メタ情報など）  
- L3：実行結果が反映（合意範囲）
- 迷ったら下げる（過大主張は事故）

### R-03 二重根拠（Two-Source Rule）でL2/L3を主張する
- “実行出力だけ” でL2/L3を主張しない
- 可能なら以下のペアを要求する：
  - 実行結果（client）＋監査ログ（control-plane）
  - 実行結果（client）＋サーバログ（server）

### R-04 相関キーを優先し、無ければ確度を落とす
- request_id / correlation_id / eventRecordId / session_id が取れない場合、
  時刻±1h で突合し、`time_confidence` を下げる（BASICS11-04）

---

## 3. 典型誤読パターン（Misreads）と回避策

## MR-01 「接続できた」＝「権限がある」
### 起きやすい場面
- SMB：ポート疎通・セッション試行
- RDP：接続開始（ネゴシエーション）
- HTTP：200/302が返っただけ

### 誤読の形
- “つながった”だけで「アクセスできる」「横展開できる」と言ってしまう

### 回避策（チェック）
- 成功条件を “操作成立” にする
  - SMB：共有列挙/アクセスの事実（LM-LOGが望ましい）
  - RDP：ログオン成功（logon_type含む）
  - HTTP：認可されたデータ取得（メタ情報でも可）
- Evidence：
  - LM-LOG（server）または CLOUD-AUDIT（control-plane）を優先
  - client出力は補助

---

## MR-02 「302/redirect」＝「認証成功」
### 起きやすい場面
- SSO（SAML/OIDC）での redirect chain
- callback URL への遷移

### 誤読の形
- redirectが起きた時点で “ログインできた” と判断

### 回避策（チェック）
- 成功条件を “サインイン成功ログ” に置く
  - AUTH-LOG（IdP/Entra/VPN/VDI）で成功を確認
  - CA評価（MFA要求/適用ポリシー）も確認
- HTTP-EVID は “フローの構造” を示す補助に留める

---

## MR-03 「Cookie/Token が見えた」＝「利用できる（権限がある）」
### 起きやすい場面
- ProxyでSet-Cookieを観測
- OAuth/OIDCでアクセストークンが返る

### 誤読の形
- トークンが返る＝そのままAPIが叩ける、と思い込む

### 回避策（チェック）
- L2の最小サンプル（API-SAMPLE）で“実際にアクセスできる”を確認する
- ただし過収集禁止：
  - 本文/添付は取らず、メタ情報のみ
- 監査ログ（CLOUD-AUDIT）と突合し、request_id/correlation_id を確保する

---

## MR-04 「列挙できた」＝「過権限／脆弱」
### 起きやすい場面
- LDAP/ADの探索
- APIで一覧取得ができる

### 誤読の形
- 列挙結果の存在だけでリスク断定（Finding化）

### 回避策（チェック）
- まず Observation として置き、Finding化は以下が揃ってから：
  - 前提（権限・ポリシー・意図）を確認
  - 最小影響（メタ情報）で成立するか確認（L2）
  - 権限根拠（PRIV）を添える
- “本当に問題か”は 12_attack-matrices 側で分類軸（重大度要素）を使う想定

---

## MR-05 「監査ログがある」＝「検知できる」
### 起きやすい場面
- Entra Audit/Sign-in が取れる
- Windowsログが取れる

### 誤読の形
- 監査ログが記録される＝SOCが検知する、と思い込む

### 回避策（チェック）
- DFIRは必ず三択で結論を出す（yes/no/unknown）
  - observed_by_blue: yes|no|unknown（06/03テンプレ）
- “検知”は以下を確認できた時だけ yes：
  - アラート/ルール/通知経路（SIEM/EDR）が存在し、追跡可能
- 無ければ「記録されるが検知されない」等、分けて書く

---

## MR-06 「401/403」＝「防御できている（安全）」
### 起きやすい場面
- APIアクセスで 401/403 が返る
- WAFでブロック

### 誤読の形
- ブロックされた＝安全、と判断

### 回避策（チェック）
- 401/403 は “その条件では拒否された” に過ぎない
- 追加観測：
  - CAの例外、別ロール、別経路（Proxy/端末条件）で変わらないか
- 結論は L1（観測）で留め、過大評価しない

---

## MR-07 「単一ソースの結果」＝「確定」
### 起きやすい場面
- ツール出力だけで成功/失敗判断
- パケットだけでサーバ処理を断定

### 回避策（チェック）
- L2/L3は二重根拠（R-03）
- 取れない場合は claim を下げる（L1）＋unknown明記

---

## 4. 誤読を防ぐ“最小チェックリスト”（実務用）
~~~~
Misread-Guard (before claiming L2/L3):
- [ ] success_criteria をログ/フィールドで定義したか
- [ ] claim_level は適切か（迷ったら下げたか）
- [ ] 二重根拠（client＋server/control-plane）が揃っているか
- [ ] 相関キー（request_id/eventRecordId/session_id）があるか
- [ ] 無い場合、time_confidence を下げ、unknown/制約を明記したか
- [ ] 過収集（本文/PII/秘密）をしていないか（ROE遵守）
~~~~

---

## 5. 11_tools-lab / DFIR / 本編への接続（どこへ戻すか）
- 誤読を見つけたら：
  - Run Log（11/02）の `success_criteria` を修正
  - Evidence Index の `claim_level` を下げる/注記する
  - DFIR結論を yes/no/unknown で確定（06/03テンプレ）
  - 必要なら REW を起票（11/05→STEP5-14）

---

## 6. 次のステップ（基礎→次フォルダへ）
06_os-network-protocol の基礎部品（00〜05）が揃った。次は、検知評価を実務形式に落とすため 08_blue-dfir に進む。

- 次：keda-lab/08_blue-dfir/00_index.md
  - 08の目的（DFIR章をEvidenceで機械的に埋める）
  - Hop別ログ（オンプレ/クラウド）と“追跡可能性”の型
  - REW/CC（STEP5-14）との接続

<<<END>>>
