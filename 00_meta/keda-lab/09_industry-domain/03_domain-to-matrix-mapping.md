<<<BEGIN>>>
# 09_industry-domain / BASICS09-03 ドメイン→マトリクス変換（DC入力→優先TTP選定→ASVS/WSTG/PTES/ATT&CKへ接続）

## 1. 目的（本編STEPへの地続き）
本ファイルは、BASICS09-01/02 で作った Domain Claim（DC-###）を入力に、
12_attack-matrices と 13_chains に流し込むための **“変換ルール（mapping spec）”** を固定する。

- 業界ドメインの前提（DC-###）を、優先TTP（攻撃手法）と検証対象（機能/資産）に変換する
- ASVS/WSTG/PTES/ATT&CK を “参照列” ではなく “優先順位ロジック” として使う
- 停止制約・法務制約・第三者接続などを、テスト計画（allowed/not_allowed/must_coordinate）へ落とす
- unknown（未検証DC）を “そのまま” マトリクスに混ぜない → 先にREQ化して確度を上げる

接続先：
- STEP1〜5：Recon→Auth→AD→Cloud→Pentest Chain の優先順位をドメインで補正
- 08_blue-dfir：P0/P1/P2（認証/権限/監査）の根拠をDCで固定
- 12_attack-matrices：業界別/組織別のTTPマトリクスを生成
- 13_chains：Chain Card（Hop/イベント）をドメイン前提で組み立てる

---

## 2. 入力/出力（固定）
### 2-1. 入力（必須）
- Domain Claims Register（DC-###）
  - validated: yes/no/unknown
  - category: REG/DATA/AVAIL/TRUST/ID/ARCH/OPS/BIZ
  - testing_implications（allowed/not_allowed/must_coordinate）
- Business Flow Cards（BF-###）※推奨
- Source Registry（S-###）

### 2-2. 出力（必須）
- Matrix Mapping Set（MMS-###）
  - DC → 優先TTP → 対象資産/機能 → 参照（ASVS/WSTG/PTES/ATT&CK）
  - テスト制約（allowed/not_allowed/must_coordinate）
  - unknown のREQトリガ（STEP5-14へ）

出力先（想定）：
- keda-lab/12_attack-matrices/（業界別・案件別のマトリクス）
- keda-lab/13_chains/（ドメイン補正済みチェーン）

---

## 3. 変換の基本原則（固定）
### 3-1. validated=yes だけが “優先TTP” を確定できる
- DC.validated=yes：優先TTPに反映（weightを付けて上げる）
- DC.validated=no：TTPの優先度を下げる（または除外）
- DC.validated=unknown：TTPを “仮説” として候補に残すことはできるが、
  - マトリクス上は `status=hypothesis` として分離
  - 先に REW/CC（REQ）で確度を上げる（unknown放置禁止）

### 3-2. “参照フレームワーク” は目的別に使い分ける
- ASVS：アプリ設計・要件（何が満たされるべきか）
- WSTG：テスト観点・手順（どう検証するか）
- PTES：評価プロセス（スコープ・制約・報告）
- ATT&CK：攻撃者TTP（チェーンの現実性・検知/対応へ接続）

### 3-3. Hopベースで整合させる
- 優先TTPは Hop1〜7（攻撃チェーン）へ必ず割り当てる
- 08_blue-dfir の観測優先（Hop2/5/7=P0）は、DC（ID/REG/OPS）で裏付ける

---

## 4. 重み付け（prioritization）ルール（固定）
TTPの優先度は “直感” ではなく、DCカテゴリとBIZ影響で決める。

### 4-1. DCカテゴリ→Hop優先の基本対応
- ID（認証/SSO/MFA）→ Hop2（P0）
- REG（監査/規制）→ Hop5/7（P0：監査・変更）
- OPS（運用/変更管理）→ Hop7（P0）＋unknown増減に直結
- ARCH（分離/セグメント/クラウド構成）→ Hop3/4（P1）
- TRUST（第三者/委託）→ Hop1（P1）＋横断（責任分界）
- DATA（データ分類/所在）→ Hop6（P2→P1に上がり得る）
- AVAIL（停止制約）→ テスト制約（not_allowed/must_coordinate）に直結
- BIZ（業務優先/Crown Jewels）→ Hop5/6 の影響説明（Impact）に直結

### 4-2. Priority算出（簡易スコア：固定）
- base_priority_by_category:
  - ID/REG/OPS = 3
  - ARCH/TRUST = 2
  - DATA/BIZ = 2（Crown Jewel該当なら3へ）
  - AVAIL = 制約フラグ（優先度ではなく計画へ影響）
- multiplier:
  - Crown Jewelに触れる BF-### がある → ×1.5
  - 第三者境界を跨ぐ → ×1.3
  - 監査要件が強い（ログ保持/証跡必須）→ ×1.2
- output_priority:
  - 4.0以上：P0（最優先）
  - 2.5〜3.9：P1
  - 〜2.4：P2
（数値は “分類を固定するための近似”。案件で調整する場合も DC の注記で根拠を残す）

---

## 5. マッピング出力（MMS-###）テンプレ（固定）
### 5-1. 最小版（必須）
~~~~
Matrix Mapping Set: MMS-###
- input_claims: [DC-###, ...]
- scope: (対象組織/システム/フロー)
- generated_at: YYYY-MM-DD

- mappings:
  - mapping_id: MAP-###
    hop: HopN
    priority: P0|P1|P2
    status: confirmed|hypothesis|excluded
    rationale:
      - (DC-###に基づく理由：1-2行)
    target_surface:
      - type: web_app|api|idp|ad|cloud|endpoint|network|third_party
        asset_or_function: <...>
    ttp:
      - framework: ATT&CK|PTES|WSTG|ASVS
        id: <...>
        name: <...>
    testing_plan:
      allowed:
        - ...
      not_allowed:
        - ...
      must_coordinate:
        - ...
    evidence_or_sources:
      claims: [DC-###]
      sources: [S-###]
    unknown_handling (if status=hypothesis):
      rew_cc_request: (何を確認すればconfirmedになるか)
      owner: client|both
      deadline_type: D1|D2|D3|D4
~~~~

### 5-2. “参照の付け方” の固定
- ASVS は “要件” として紐付ける（例：認証、アクセス制御、ログ、暗号）
- WSTG は “検証手順” として紐付ける（例：認証テスト、セッション、アクセス制御）
- PTES は “工程” として紐付ける（スコープ/ルール/報告）
- ATT&CK は “攻撃者の具体TTP” として紐付ける（チェーン/検知へ）

（注：IDの具体番号は、後続の 12_attack-matrices のファイルで “確定” させる。ここは変換仕様）

---

## 6. unknown（仮説）を扱うルール（固定）
### 6-1. 仮説TTPは “別枠” で保持する
- confirmed（validated=yesのみ）
- hypothesis（validated=unknown由来）
- excluded（validated=no由来、または制約で不可）

### 6-2. 仮説を confirmed に上げる条件
- DC-### が yes/no に確定する（S-###取得）
- あるいは Evidence（E-###）相当の一次証跡で裏が取れる（09のSourceとして扱う）

### 6-3. unknown放置禁止（必ずREQ化）
- hypothesis には必ず `unknown_handling.rew_cc_request` を付与し、
  - REQ-###（STEP5-14）へ変換できる文章で書く

---

## 7. テスト制約（AVAIL/REG/法務）を計画へ落とす（固定）
### 7-1. not_allowed の固定例
- 重要サービスに対する負荷試験/DoS
- 本番データの持ち出し（PII/機密）
- 第三者接続先への無許可テスト

### 7-2. must_coordinate の固定例
- 運用窓のみでの実施（変更凍結、夜間）
- 監査/法務の事前承認が必要
- Blueの立会いが必要（検知テスト、ログ抽出）

### 7-3. allowed の固定例
- 影響の低い環境/時間帯での検証
- テストアカウントでの検証
- 監査ログのエクスポート（必要範囲のみ）

---

## 8. “業務フロー（BF）” との結合ルール（固定）
- BF-### の Crown Jewel touchpoints を “target_surface” の優先順位へ反映
- 例：Payment Authorization の step で API が Crown Jewel に触れる
  → Hop2（認証）と Hop6（データ/取引操作）の優先度を上げる
- BF の trust_boundary（partner）を TRUSTカテゴリの multiplier として扱う

---

## 9. 08_blue-dfir との整合（固定）
- P0（Hop2/5/7）は “DFIR上の観測優先” と “攻撃チェーン上の優先” が一致しやすい
- ただし、DCで停止制約が強い場合（AVAIL=yes）は、検知/監査の検証順序を調整する
  - 先にログ抽出（REQ-LOG）を確定し、負荷のあるテストは後回し/別環境へ

---

## 10. 次のファイル（BASICS09-04）への接続
次は、MMS（マッピングセット）を “実ファイルのマトリクス” として出力するためのテンプレを用意する。
（12_attack-matrices の入力テンプレ）

- 次：keda-lab/09_industry-domain/04_matrix-output-template.md
  - 12_attack-matrices に配置するマトリクスの列定義
  - Hop/優先度/対象/ATT&CK/WSTG/ASVS/PTES の並び
  - confirmed/hypothesis/excluded の扱い
  - Evidence/Source参照欄（DC/S/E）
<<<END>>>
