フォルダ名: keda-lab/06_os-network-protocol/
ファイル名: 00_index.md
STEP名: BASICS06-00 Index（目的・読む順・本編01〜05への刺さり先・このフォルダの完成像）

<<<BEGIN>>>
# 06_os-network-protocol / BASICS06-00 Index（目的・読む順・本編01〜05への刺さり先・このフォルダの完成像）

## 1. このフォルダは何をする場所か（1行）
06_os-network-protocol は、**通信・OS・プロトコルの“観測点（ログ/フィールド/相関キー）”を固定し、STEP3/5の主張をEvidenceとして成立させる辞書**である。

---

## 2. どの本編STEPを補うか（刺さり先）
- STEP1（01_asm-recon）
  - Recon の仮説（入口/露出/到達）を “通信・観測” で裏付け、OSINT/HTTP-EVIDの根拠を強くする
- STEP2（02_auth-iam-sso-mfa）
  - SSO/認証フローの誤読を防ぎ、制御（MFA/CA）の主張をログ突合で支える
- STEP3（03_ad-kerberos-lateral）
  - LDAP/Kerberos/SMB/RDP などの列挙・横展開を「成功/失敗の定義」「最小ログ」「相関キー」で根拠化する
- STEP4（04_cloud）
  - オンプレ→クラウド接続（Proxy/IdP/同期）の観測点を整理し、監査ログとの突合を可能にする
- STEP5（05_pentest-chain）
  - Evidence Index / Timeline / DFIR章を“観測の事実”から機械的に埋められるようにする

---

## 3. このフォルダで最終的に“何ができる状態”を作るか（完成像）
### 3-1. 実施時の状態（観測）
- 「何が起きたか」を、ログ/相関キーで説明できる
- “接続できた＝成功” などの誤主張が起きない（成功条件を固定）
- ログ欠落時のフォールバック（パケット等）を選べる

### 3-2. 提出時の状態（Evidence/DFIR）
- Timeline の因果が “時刻＋相関キー” で成立している
- DFIR章で「検知できた/できない/不明」を根拠付きで書ける
- Evidence Pack の sanitized に “Fact” として貼れる観測結果が揃う

---

## 4. 読む順番（推奨：この順に読むと迷子にならない）
1) `01_overview.md`  
   - 06フォルダの役割（観測点辞書）と本編への接続思想
2) `02_protocol-observation-points.md`  
   - DNS/HTTP/TLS/LDAP/Kerberos/SMB/RDP/WinRM の最小観測点（Evidence化の要件）
3) `03_log-and-correlation-cheatsheet.md`  
   - Hop別の最低限ログ＆相関キー（Run Log / Evidence Index / Timeline へ直結）
4) `04_minimal-packet-vs-log-evidence.md`（今後作成）  
   - “ログ優先/パケット補助”の原則と例外、ログ欠落時のフォールバック設計
5) `05_common-misreads-and-how-to-avoid.md`（今後作成）  
   - 誤読パターン（接続＝成功など）と回避策（L1/L2/L3の誤主張防止）

---

## 5. “使う時”の最短手順（実務フロー：迷わない運用）
### 5-1. 観測設計（事前）
- 対象Hopを決める（Hop1〜Hop7）
- `03_log-and-correlation-cheatsheet.md` で “Primaryログ/相関キー” を決める
- 取れる/取れない（権限・保持）を確認し、取れないならフォールバックを先に決める

### 5-2. 実行中（Run）
- 実行ログ（11_tools-lab/02）に相関キーを残す
- Evidence化する際は `02_protocol-observation-points.md` の “最小フィールド” を満たす形に整える

### 5-3. DFIR（検知評価）
- Blue側ログがある：観測できた/できないを根拠ログで確定
- ログが無い：unknown として明記し、ログ保持/取得を REW で要求（STEP5-14）

---

## 6. このフォルダが“扱う範囲”と“扱わない範囲”（境界）
### 6-1. 扱う（ここに置く）
- 観測点（最小ログ/最小フィールド/相関キー）
- 成功条件の定義（誤主張防止）
- ログとパケットの使い分け（Evidence設計）

### 6-2. 扱わない（別に置く）
- 具体ツールの手順（コマンド、インストール、設定）
  - 11_tools-lab/lab-notes に置く
- 攻撃チェーンの物語や報告書テンプレ
  - 05_pentest-chain に置く

---

## 7. 今後追加する（拡張計画：ロードマップ）
- `04_minimal-packet-vs-log-evidence.md`  
  - ケース別（HTTP/SSO、SMB/RDP、LDAP/Kerberos、Cloud API）の主根拠設計
- `05_common-misreads-and-how-to-avoid.md`  
  - 誤読→事故（L2誤主張、過収集、ログ欠落）の典型をまとめ、ガードレールへ接続
- （必要に応じて）`06_reference-log-fields.md`  
  - 主要ログのフィールド辞書（Evidenceに残す最小項目）

---

## 8. 関連リンク（本編との接続点）
- Hop/チェーン運用：`keda-lab/05_pentest-chain/01〜15_*`
- Evidence運用：`keda-lab/11_tools-lab/*`（Run Log/時刻/ハッシュ）
- Must Evidence：`keda-lab/05_pentest-chain/12_*`
- 早期通知（REW/CC）：`keda-lab/05_pentest-chain/14_*`

<<<END>>>
