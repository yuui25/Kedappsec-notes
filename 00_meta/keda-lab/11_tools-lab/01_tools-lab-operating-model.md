<<<BEGIN>>>
# 11_tools-lab / BASICS11-01 Tools-Lab 運用モデル（ツール＝Evidence製造機として本編01〜05へ接続）

## 1. 目的（11_tools-lab が補う本編STEP）
11_tools-lab は「便利ツール集」ではなく、**本編01〜05の成果物を“再現可能・監査可能”にするための運用基盤**である。

- 補強対象（本編への接続）
  - STEP1（01_asm-recon）：Reconの出力を “根拠付き” で残す（再調査できる）
  - STEP2（02_auth-iam-sso-mfa）：認証・セッション・ポリシー観測を証拠化する
  - STEP3（03_ad-kerberos-lateral）：列挙/横展開/権限変化を“実行ログ＋結果＋根拠”で残す
  - STEP4（04_cloud）：監査ログ/ロール/Consent/API最小サンプルを“L1/L2/L3”で管理する
  - STEP5（05_pentest-chain）：Evidence Index / Timeline / Report が破綻しないようにする（STEP5-07〜15を支える）

---

## 2. Tools-Lab の基本原則（固定）
### 2-1. 「ツール」ではなく「Evidence 製造機」として扱う
- すべてのツール実行は **Evidence を生む** 前提で設計する
- “何をしたか” ではなく “何が証明できるか” を先に定義する

### 2-2. 実行ログの3点セット（必須）
どのツールでも、最低限以下を揃える。

1) 実行条件（入力・対象・アカウント/ロール・時刻）  
2) 実行出力（結果）  
3) 根拠（ログ/監査/画面/相関キー：取れる範囲で）

※この3点が揃うと、STEP5-09/10 の Evidence Index / Timeline に機械的に入る。

### 2-3. データ最小化（固定）
- “取り過ぎない” を先に決め、必要最小のサンプリングで L2 を狙う
- 機微情報は raw に隔離し、sanitized を本文引用用とする（STEP5-07準拠）

---

## 3. 成果物（11_tools-lab が提供するもの）
本フォルダで最終的に整備する成果物は以下。

- ツール→Evidence 種類→本編Hop（Hop1〜7）への対応表
- 実行ログテンプレ（共通フォーマット）
- 保存・命名規則（Evidence ID、raw/sanitized、ハッシュ）
- 失敗パターン集（誤検知、過収集、ログ欠落、時刻ずれ）
- “安全にやるための制約”（ROE/スロットリング/アクセス制御）

---

## 4. フォルダ内の標準構造（推奨）
~~~~
keda-lab/11_tools-lab/
  01_tools-lab-operating-model.md
  02_execution-log-template.md
  03_tool-to-evidence-mapping.md
  04_time-sync-and-hash-policy.md
  05_failure-modes-and-guardrails.md
  06_minimal-commands-by-hop.md
  lab-notes/
    (個別ツールの検証メモ、PoC、設定例)
~~~~

---

## 5. ツール実行を “本編Hop” に接続するルール（最重要）
### 5-1. Hop別：ツール実行の目的（何を証明するか）
- Hop1（Recon）：入口・露出・到達可能性（主にL1）
- Hop2（社員化/セッション）：サインイン/CA/MFA/到達範囲（L1〜L2）
- Hop3（AD Discovery）：構造・高価値ターゲット・委任/権限の兆候（L1〜L2）
- Hop4（材料）：次Hopを可能にする材料の存在（L1〜L2、機微は隔離）
- Hop5（横展開）：到達と権限変化（L2〜L3、ログ根拠が重要）
- Hop6（Hybrid）：Connect/同期・波及理由（L1〜L2、監査ログが鍵）
- Hop7（Cloud）：監査ログ＋API最小サンプル（L1〜L2中心、L3は合意範囲のみ）

### 5-2. Claim（L1/L2/L3）を実行前に決める
- L1 Observed：設定/ログで観測（実行しない）
- L2 Demonstrated：最小サンプルで成立を示す（推奨）
- L3 Executed：合意範囲で実行し結果を得る（必要最小）

---

## 6. 実行ログテンプレ（最小形：全ツール共通）
このテンプレを満たす実行ログは、そのまま Evidence Index（STEP5-09）へ転記できる。

### 6-1. 実行ログ（共通）
- run_id：RUN-YYYYMMDD-###  
- hop：HopN
- scenario：A/B/C/D/E（該当する場合）
- 목적（証明したいこと）：  
- claim_level_target：L1/L2/L3（狙い）
- actor：user/role/tester
- target：host/url/resource
- time_start_jst：YYYY-MM-DDTHH:MM:SS+09:00
- time_end_jst：YYYY-MM-DDTHH:MM:SS+09:00
- tool_name/version：
- command_or_action（要約、機微は伏せる）：
- output_summary（1行）：
- correlation_key（あれば）：
- evidence_ids（後で採番して紐付け）：
- notes（安全配慮、非実行理由など）：

### 6-2. Evidence 化（保存ルール）
- raw：完全版（機微含む可能性）
- sanitized：本文引用用（マスキング済み）
- sha256：両方（存在する方）に付与
- Evidence Index に 1 Evidence = 1 行で登録（STEP5-09準拠）

---

## 7. 典型 “ツール→Evidence→章” の接続例（短例）
### 例：Cloud 監査ログ抽出（Hop7）
- ツール行為：Entra Audit/Sign-in を期間指定で抽出
- Evidence：`CLOUD-AUDIT`（ログ抜粋 + 取得条件 + 時刻）
- Report 接続：
  - Kill Chain（KC）：Hop7の事実（L1）
  - DFIR（DF）：検知/追跡可否の根拠
  - Executive（ES）：主張の根拠（L1止まり/引き上げ可否）

### 例：AD 構造要約（Hop3）
- ツール行為：OU/Group/重要サーバ候補の要約生成
- Evidence：`AD-DISC`（要約＋根拠出力）
- Report 接続：
  - KC：次Hopの宛先選定理由
  - Findings：過剰権限・委任などの候補（ただしEvidence不足ならObservation）

---

## 8. このフォルダを “開始” する手順（今日やるべき最小）
本編STEP5の運用（Evidence/Report）を壊さないため、まず以下を固定する。

1) 実行ログテンプレ（次ファイル）を確定  
2) ツール→Evidence対応表を作成（Hop別）  
3) 時刻（JST/ISO）とハッシュ運用を固定  
4) 失敗パターン（ログ欠落/時刻ずれ）を先に潰す  

---

## 9. 次のファイル（BASICS11-02）への接続
次は、実務で毎回使う「実行ログテンプレ」をファイルとして確定する。

- 次：keda-lab/11_tools-lab/02_execution-log-template.md
  - 実行ログの詳細テンプレ（コピペ運用）
  - Evidence Index / Timeline への転記手順
  - raw/sanitized の作り分け（最小マスキング指針）

<<<END>>>
