フォルダ名: keda-lab/11_tools-lab/
ファイル名: 00_index.md
STEP名: BASICS11-00 Index（目的・読む順・本編01〜05への刺さり先・このフォルダの完成像）

<<<BEGIN>>>
# 11_tools-lab / BASICS11-00 Index（目的・読む順・本編01〜05への刺さり先・このフォルダの完成像）

## 1. このフォルダは何をする場所か（1行）
11_tools-lab は、**STEP1〜5で使うツールを「Evidence 製造機」として運用し、Evidence Index / Timeline / Report に確実に流すための共通基盤**である。

---

## 2. どの本編STEPを補うか（刺さり先）
- STEP1（01_asm-recon）
  - Recon の出力を “根拠付き・再調査可能” にする（OSINT/HTTP-EVIDのEvidence化）
- STEP2（02_auth-iam-sso-mfa）
  - 認証/セッション/CA の観測を「L1/L2の主張」に落とし、ログ欠落を防ぐ
- STEP3（03_ad-kerberos-lateral）
  - 列挙/横展開の結果を “実行ログ＋根拠ログ” として残し、誤主張を防ぐ
- STEP4（04_cloud）
  - 監査ログ・ロール・API最小サンプルを Evidence 化し、Cloud終端の主張強度を上げる
- STEP5（05_pentest-chain）
  - Evidence Index / Timeline / Report を「データから埋める」前提を作り、提出物の監査性（改ざん検知）を担保する

---

## 3. このフォルダで最終的に“何ができる状態”を作るか（完成像）
### 3-1. 実施時の状態（運用）
- Hopごとに「最小実行セット」を回せる
- 実行ごとに Run Log をテンプレで埋められる
- Evidence が raw/sanitized で保存され、時刻統一・ハッシュで監査性がある
- Evidence Index / Timeline が機械的に埋まる
- 失敗（欠落/過収集/時刻ズレ/ROE逸脱）が早期に検知され、REW/CCへ繋がる

### 3-2. 提出時の状態（成果物）
- Evidence Pack が “本文引用用(sanitized)＋隔離(raw)” で整っている
- hash-manifest により改ざん検知ができる
- Report の各主張が Evidence ID で追跡できる

---

## 4. 読む順番（推奨：この順に読むと迷子にならない）
1) `01_tools-lab-operating-model.md`  
   - このフォルダの役割と、01〜05本編への接続思想
2) `02_execution-log-template.md`  
   - 実行ログ（Run Log）のテンプレ：ここが運用の中心
3) `03_tool-to-evidence-mapping.md`  
   - ツール/手段→Evidence→Hop→Report章の対応表（迷子防止）
4) `04_time-sync-and-hash-policy.md`  
   - 時刻統一＋ハッシュ：相関と監査性（Evidence Packの信頼性）
5) `05_failure-modes-and-guardrails.md`  
   - 失敗パターンとガードレール：事故防止（過収集/欠落/ROE）
6) `06_minimal-commands-by-hop.md`  
   - Hop別の最小実行セット：Must Evidence を取り逃さない

---

## 5. “使う時”の最短手順（実務フロー：コピペで回す）
### 5-1. 開始前（Preflight：必須）
- 対象シナリオ（A〜E）を決める（STEP5-02）
- Must Evidence（STEP5-12のM）を抽出し、取得可否（Y/N/U）を付ける
- 基準時計（Primary Clock）を決める（`04_time-sync-and-hash-policy.md`）
- ROE（非実行範囲/データ最小化）をEvidence化する（STEP5-13/本編ROE）

### 5-2. 実行（Run）
- 1アクションごとに Run Log（`02_execution-log-template.md`）を埋める
- raw/sanitized を保存し、Evidence ID を採番する
- Evidence Index（STEP5-09）へ 1 Evidence=1行 で登録する
- Timeline（STEP5-09）へイベント登録する（narrative_1lineを作る）

### 5-3. 日次（Quality Gate）
- Must Evidence の充足率を確認する（埋まらないなら REW を起票：STEP5-14）
- secrets/PII 混入がないか sanitized をレビューする
- hash-manifest（必要なら daily）を更新する

---

## 6. このフォルダが“扱う範囲”と“扱わない範囲”（境界を明確化）
### 6-1. 扱う（ここに置く）
- ツール横断の共通運用（Run Log、Evidence化、命名、時刻、ハッシュ、ガードレール）
- Hop別の最小実行セット（何を取ればMustが埋まるか）

### 6-2. 扱わない（別に置く）
- ツール個別の詳細手順（インストール、詳細設定、具体コマンド）
  - これは `11_tools-lab/lab-notes/` に置く（本編を肥大化させない）
- 技術解説（プロトコル理解、ログ項目の意味）
  - これは `06_os-network-protocol/` に置く（観測点辞書）

---

## 7. 今後追加する（拡張計画：ロードマップ）
### 7-1. lab-notes（ツール個別手順の保管庫）を作る
- 目的：STEP1〜5で実際に使える “手順” を提供する（Evidence直結で）

推奨構造：
~~~~
keda-lab/11_tools-lab/lab-notes/
  tool-<name>/
    01_install-and-safe-config.md
    02_minimal-commands-by-hop.md
    03_minimal-output-and-sampling.md
    04_evidence-examples-raw-sanitized.md
    05_common-failures-and-fixes.md
~~~~

### 7-2. 優先して追加するEvidence領域（Must優先）
1) CLOUD-AUDIT / AUTH-LOG / CA-POL（取り逃しが致命的）  
2) API-SAMPLE / PRIV（Role/Consent）  
3) LM-LOG / AD-DISC  
4) OSINT / HTTP-EVID  

---

## 8. 関連リンク（本編との接続点）
- Evidence Index / Timeline：`keda-lab/05_pentest-chain/09_*`（設計）
- Must Evidence マトリクス：`keda-lab/05_pentest-chain/12_*`
- 欠落フォールバック：`keda-lab/05_pentest-chain/11_*`
- 実施中コミュニケーション（REW/CC）：`keda-lab/05_pentest-chain/14_*`
- 提出パッケージ：`keda-lab/05_pentest-chain/15_*`

<<<END>>>
