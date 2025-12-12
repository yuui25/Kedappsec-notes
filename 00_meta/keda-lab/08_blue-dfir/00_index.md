
<<<BEGIN>>>
# 08_blue-dfir / BASICS08-00 Index（DFIR章をEvidenceで機械的に埋める：目的・読む順・本編01〜05への刺さり先）

## 1. このフォルダは何をする場所か（1行）
08_blue-dfir は、**攻撃チェーン（Hop1〜7）に対して「Blue側が何を観測できるか／できないか」をEvidenceで確定し、STEP5のDFIR章を機械的に作れる状態**を作る。

---

## 2. どの本編STEPを補うか（刺さり先）
- STEP2（02_auth-iam-sso-mfa）
  - 認証・MFA・CA の “検知/追跡可能性” をログ根拠で確定する
- STEP3（03_ad-kerberos-lateral）
  - AD探索/横展開（LDAP/Kerberos/SMB/RDP等）が Blue に見えるかを確定する
- STEP4（04_cloud）
  - Entra/クラウド管理操作/APIアクセスが “監査ログと検知” のどちらで担保されているかを確定する
- STEP5（05_pentest-chain）
  - DFIR章（検知評価・ログギャップ・改善提案）を Evidence ID で追跡できる形にする
  - REW/CC（STEP5-14）を “ログ欠落・保持不足” 起点で発行できるようにする

---

## 3. このフォルダで最終的に“何ができる状態”を作るか（完成像）
### 3-1. 実施時（Blue観測）
- Hop別に「Blueが観測できるイベント種類」を把握できる
- 相関キー（request_id/eventRecordId/session_id）を、どのログで拾うかが決まっている
- “ログが無い”を早期に発見し、REW/CCで顧客アクションを要求できる

### 3-2. 提出時（DFIR章）
- 各Hopについて、必ず以下の結論を根拠付きで書ける：
  - observed_by_blue: yes / no / unknown
  - evidence_basis: E-###（または log_not_available）
  - what_blue_can_see（最小の追跡可能性）
  - gaps（不足ログ/保持/相関キー）
  - recommendation（改善提案）
- DFIR章が “推測” ではなく “観測可能性の事実” になる

---

## 4. 読む順番（推奨）
1) `01_dfir-conclusion-template.md`（次に作成）
   - DFIR章の結論テンプレ（yes/no/unknown を必ず出す型）
2) `02_log-sources-by-hop.md`（次に作成）
   - Hop別：Primary/Secondary ログソース（オンプレ/クラウド）
3) `03_detection-vs-audit.md`（次に作成）
   - “監査ログがある”と“検知できる”の違いを整理（誤読防止）
4) `04_log-gap-playbook.md`（次に作成）
   - ログ欠落時の対処（保持延長、権限付与、エクスポート手順、REW/CC）
5) `05_recommendation-catalog.md`（次に作成）
   - 改善提案の定型（ログ保持、相関キー、アラート、SIEM連携）

---

## 5. “使う時”の最短手順（実務フロー）
### 5-1. 事前（Day1）
- Hop別に “最低限取れるログ” を確認（`02_log-sources-by-hop.md`）
- 取れない場合はフォールバックを決め、REW/CC準備（STEP5-14）

### 5-2. 実行中（Run）
- Run Log（11_tools-lab/02）に相関キーを残す
- Blueログで対応するイベントを抽出し、Evidence化（CLOUD-AUDIT/LM-LOG/DFIR）

### 5-3. 日次（Quality Gate）
- observed_by_blue を暫定で埋める（unknown を恐れない）
- unknown の理由（保持/権限/設計）を明確化し、顧客へ要求を出す（REW）

---

## 6. このフォルダが“扱う範囲”と“扱わない範囲”（境界）
### 6-1. 扱う（ここに置く）
- DFIR章の型（結論・根拠・ギャップ・提案）
- Hop別ログソースと相関キー
- 検知（Detection）と監査（Audit）の区別
- ログ欠落時のプレイブック（REW/CC連携）

### 6-2. 扱わない（別に置く）
- 具体的な攻撃手順：`05_pentest-chain` / 本編各STEP
- ツールの詳細手順：`11_tools-lab/lab-notes`
- プロトコル観測点：`06_os-network-protocol`

---

## 7. 関連リンク（本編との接続点）
- Run Log / Evidence運用：`keda-lab/11_tools-lab/*`
- 観測点・相関キー：`keda-lab/06_os-network-protocol/03_*`
- Must Evidence：`keda-lab/05_pentest-chain/12_*`
- REW/CC：`keda-lab/05_pentest-chain/14_*`
- 提出パッケージ：`keda-lab/05_pentest-chain/15_*`

---

## 8. 次のファイル（BASICS08-01）への接続
次は、DFIR章を“必ず書ける”結論テンプレを確定し、Evidence ID 参照で機械的に埋められる形にする。

- 次：keda-lab/08_blue-dfir/01_dfir-conclusion-template.md
  - yes/no/unknown を必ず出す
  - evidence_basis（E-###）を必須化
  - gaps と recommendation の定型をセット化

<<<END>>>
