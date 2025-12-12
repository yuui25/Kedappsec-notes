<<<BEGIN>>>
# 11_tools-lab / BASICS11-04 時刻統一＋ハッシュ運用（Evidence改ざん検知・相関の土台）

## 1. このファイルが補う本編STEP（01〜05への地続き）
- STEP1（Recon）：取得日時・根拠の時系列がブレず、再調査できる
- STEP2（Auth/SSO/MFA）：サインイン/CA結果を他ログと相関できる
- STEP3（AD/横展開）：端末・サーバ・DC・EDR の時刻ズレで主張が崩れる事故を防ぐ
- STEP4（Cloud）：監査ログ（Audit/Sign-in）と API 実行を確実に突合できる
- STEP5（Evidence/Report）：Evidence Pack の改ざん検知と監査性（Chain of Custody）を成立させる

---

## 2. 時刻統一ポリシー（Time Normalization Policy）

### 2-1. 標準時刻フォーマット（固定）
Evidence Index / Timeline / Run Log で使う標準は以下。

- `ISO 8601 + JSTオフセット` を必須とする  
  例：`2025-12-13T09:13:02+09:00`

補足（推奨）：
- 併記できる場合は UTC も残す（監査や相関で強い）  
  例：`2025-12-13T00:13:02Z`

---

### 2-2. “基準時計（Primary Clock）”の定義（必須）
案件ごとに基準時計を 1 つ決める。推奨は以下の順。

1) ログ集約基盤（SIEM 等）の取り込み時刻（あるなら最強）  
2) クラウド監査ログの時刻（Entra Audit/Sign-in）  
3) テスト端末の時刻（NTP同期済み）  

Evidence Index の `collected_at_iso_jst` と Timeline の `time_iso_jst` は、原則として基準時計に合わせる。

---

### 2-3. 端末・基盤の時刻整合（運用ルール）
- テスト端末は NTP 同期を前提（開始前に確認）
- “時刻ズレの兆候” が出た場合は、その時点で **Risk Early Warning（STEP5-14）** と同等に扱う  
  - 例：クラウド監査ログと端末実行時刻が継続的に数分ズレる
- ログ取得元が複数タイムゾーンの場合、**変換規則（TZ変換）を案件メモとして Evidence 化**する（ROEと同格）

---

### 2-4. 相関（Correlation）に必要な最小フィールド（固定）
時刻相関だけに依存しないため、可能な限り以下を収集する（存在する場合のみ）。

- `request_id` / `correlation_id`（クラウド/アプリ）
- `eventRecordId`（Windows 系）
- `session_id`（VDI/VPN/SSO）
- `trace_id`（アプリ/APM）

Run Log の `correlation_key` に必ず残す（BASICS11-02準拠）。

---

### 2-5. “時刻の確度”を明示する（推奨）
Evidence Index に以下の運用カラム（推奨）を追加してもよい。

- `time_confidence`：`high / medium / low`
  - high：基準時計＋相関IDあり
  - medium：基準時計のみ
  - low：端末時計のみ、またはズレ疑いあり

※Report の主張強度（L1/L2/L3）と混ぜない。別軸として扱う。

---

## 3. ハッシュ運用ポリシー（Hash Manifest Policy）

### 3-1. 目的（固定）
- Evidence の **改ざん検知**
- 提出パッケージ（STEP5-15）の **監査性**
- raw/sanitized の対応関係の **追跡**

---

### 3-2. いつハッシュを取るか（必須）
Evidence を保存する運用の “節目” でハッシュを取る。最低限以下。

1) raw 保存直後（初回確定）  
2) sanitized 作成直後（本文引用版の確定）  
3) 提出前（Evidence Pack final の確定）  

禁止：
- 作業途中で何度もハッシュを更新して「どれが確定版か」不明にしない

---

### 3-3. hash-manifest のフォーマット（固定）
- 方式：SHA-256
- 形式：`<sha256>␠␠<relative_path>`（相対パスのみ）

例：
~~~~
# hash-manifest.txt (generated_at: 2025-12-13T10:00:00+09:00)
d2c7...  evidence/hop7_cloud_api/raw/E-070_graph-api-sample.json
a9f1...  evidence/hop7_cloud_api/sanitized/E-070_graph-api-sample.md
~~~~

運用ルール：
- 追記型（append-only）ではなく、**提出前に final を1回生成**するのが基本
- ただし日次レビューをする場合は `hash-manifest-daily/` に日付別で保存する

---

### 3-4. Evidence Index との整合（必須）
Evidence Index（STEP5-09）に以下を必ず埋める。

- `sha256_raw` / `sha256_sanitized`
- `path_raw` / `path_sanitized`

これにより、「本文→Evidence ID→Index→ファイル→ハッシュ」が一筆書きで辿れる。

---

### 3-5. raw/sanitized の派生関係（推奨：派生メタ）
sanitized 側に “派生元” を明示する（本文引用の透明性）。

sanitized の先頭に以下を入れる。
~~~~
Derived-From: evidence/hop7_cloud_api/raw/E-070_graph-api-sample.json
Derived-From-SHA256: sha256:d2c7...
Sanitized-SHA256: sha256:a9f1...
~~~~

---

## 4. “改ざん検知”のレビュー手順（提出前ゲート）
提出前（STEP5-15）に最低限以下を通す。

- [ ] Evidence Index の `path_*` が実在する
- [ ] Evidence Index の `sha256_*` が hash-manifest と一致する
- [ ] raw と sanitized が同一 Evidence ID で対応している
- [ ] 参照されていない Evidence（Indexに無いファイル）が残っていない（混入防止）
- [ ] PII/秘密情報が sanitized に出ていない（sanitized の品質）

---

## 5. “そのうち各ステップで使うツール詳細・使い方も記載するか？”への回答
結論：**記載する。** ただし置き場所と粒度を分けて、01〜05 の本編を肥大化させない。

### 5-1. 置き場所（固定方針）
- 11_tools-lab は「運用モデル・テンプレ・共通規則」を置く（このファイル群）
- **ツール個別の詳細・使い方（手順/設定/出力例）は `11_tools-lab/lab-notes/` に置く**
- 本編 01〜05 からは、必要箇所で `lab-notes` を参照する（重複記載しない）

推奨構造：
~~~~
keda-lab/11_tools-lab/lab-notes/
  tool-<name>/
    01_install-and-safe-config.md
    02_minimal-output-and-sampling.md
    03_evidence-examples-raw-sanitized.md
    04_common-failures-and-fixes.md
~~~~

### 5-2. 粒度（固定方針）
- “攻撃手順の羅列”ではなく、**Evidence を作るための最小手順**として整理する
- どの Hop / どの Evidence 種類（AUTH-LOG 等）を埋めるための手順かを明記し、STEP5-12 の `M` と接続する
- ROE/データ最小化に抵触しないサンプリング粒度を先に書く

---

## 6. 次のファイル（BASICS11-05）への接続
次は、時刻・ハッシュの運用を壊す典型失敗を先に潰す。

- 次：keda-lab/11_tools-lab/05_failure-modes-and-guardrails.md
  - 失敗パターン（時刻ズレ、過収集、誤検知、ログ欠落、機微混入）
  - ガードレール（ROE、スロットリング、隔離、レビューゲート）
  - “やらない”を決めるチェック（安全運用）

<<<END>>>
