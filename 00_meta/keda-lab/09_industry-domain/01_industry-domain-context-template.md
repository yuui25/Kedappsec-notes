<<<BEGIN>>>
# 09_industry-domain / BASICS09-01 業界ドメイン・コンテキストテンプレ（Assumption排除：yes/no/unknown＋Source IDで根拠固定）

## 1. 目的（本編STEPへの地続き）
本テンプレは、STEP1〜5（Recon→Auth→AD→Cloud→PT/TLPT）を「汎用の攻撃チェーン」ではなく、
**対象業界の制約・重要資産・業務フローに合わせて“優先順位と結論”を固定**するために使う。

- 業界ドメインの前提（例：規制、可用性要求、第三者接続、データ種別）を “推測” で書かない
- すべてのドメイン主張を `validated: yes/no/unknown` で固定し、Source IDで追跡する
- ドメイン前提を、STEP5の「攻撃チェーン主張」「DFIR章（08）」「REW/CC要求（STEP5-14）」へ接続する
- 12_attack-matrices / 13_chains で “業界別の攻撃パターン” を作る土台にする

接続先（必須）：
- 05_pentest-chain：攻撃チェーンの優先TTP、報告書のImpact（事業影響）説明
- 08_blue-dfir：観測・検知・対応の優先度（P0/P1/P2）の根拠
- 11_tools-lab：収集・相関・証跡化（Source/Evidence運用）
- 12_attack-matrices：業界別TTPマトリクスの選定
- 13_chains：業界別Chain Cardの生成

---

## 2. 運用ルール（固定）
### 2-1. 三択（yes/no/unknown）を必ず出す
- `yes`：一次情報（ドキュメント/設定/ログ/契約/担当者証言）で裏が取れている
- `no`：当該条件が成立しないことが一次情報で確定している
- `unknown`：情報不足/権限不足/守秘/未確認で判断不能（REW/CC要求に落とす）

禁止：
- “たぶん”“想定”“一般的に”で結論を確定しない → unknown を使う

### 2-2. Source ID（S-###）で根拠を固定
ドメインの主張は必ず Source ID を持つ（S-###）。

- S-### は案件単位の通し番号（S-001〜）
- Source の種類（type）を必ず持つ
  - interview（担当者ヒアリング）
  - policy（規程/ポリシー）
  - contract（契約/委託条件/規制要件）
  - architecture（構成図/設計書）
  - config（設定エクスポート）
  - log（監査ログ/運用ログ）
  - ticket（変更管理/運用チケット）
  - other（根拠を明記）

### 2-3. ドメイン主張は「台帳化」してSTEP5へ渡す
- Domain Claims Register（後述）を作り、重要な claim は Step5 の主張/Impact/REW/CC に参照させる
- unknown が残る claim は、必ず REW/CC（要求）へ落とす（放置禁止）

---

## 3. Source Registry（S-###）テンプレ（必須）
~~~~
Source Registry:
- source_id: S-001
  type: interview|policy|contract|architecture|config|log|ticket|other
  title: <短い説明>
  owner: client|tester|both
  date_obtained: YYYY-MM-DD
  confidentiality: public|internal|confidential|restricted
  location: (格納場所/リンク/チケット番号/ファイル名)
  note: (このSourceが何を裏付けるか：1-2行)
~~~~

---

## 4. Domain Claims Register（主張台帳：固定）
> ドメイン理解を “文章” ではなく “検証可能な主張” に分解する。

### 4-1. Claim テンプレ（固定）
~~~~
Domain Claim: DC-###
- statement: (ドメイン前提の主張：例「決済APIは24/365で停止不可」)
- validated: yes|no|unknown
- sources: [S-###, S-###] | []
- scope: (どの範囲の話か：組織/システム/リージョン/子会社など)
- impact_if_true:
  - (STEP5への影響：攻撃チェーン優先度、DoS禁止範囲、監査要件、報告書の影響説明 など)
- testing_implications:
  - allowed: (実施可能なテスト)
  - not_allowed: (禁止/制約)
  - must_coordinate: (事前調整が必要)
- rew_cc_trigger (if validated=unknown):
  - request: (何を追加で入手すれば yes/no に落とせるか)
  - owner: client|both
  - deadline_type: D1|D2|D3|D4
~~~~

### 4-2. Claim カテゴリ（固定）
- REG（規制/監査/コンプラ）
- DATA（データ分類/PII/機密/鍵管理）
- AVAIL（可用性/停止許容/運用窓）
- TRUST（信頼境界/第三者/委託/相互接続）
- ID（ID基盤/SSO/MFA/権限モデル）
- ARCH（アーキ/ネットワーク/セグメント/クラウド）
- OPS（運用/変更管理/監視/インシデント対応）
- BIZ（業務フロー/業務優先度/重要KPI）

---

## 5. Domain Profile（業界プロファイル：コピペ用）
> “業界” ではなく “対象組織が置かれたドメイン条件” を記述する。

~~~~
Domain Profile:
- industry: <finance|healthcare|retail|manufacturing|public|other>
- business_model_summary:
  - (提供サービス/顧客/収益モデル：1-3行)
- critical_services (Crown Jewel candidates):
  - - name: <service>
      description: <...>
      availability_requirement: high|medium|low
      validated: yes|no|unknown
      sources: [S-###]
- regulatory_and_audit_requirements:
  - - name: <framework/regulation>
      applicability: yes|no|unknown
      sources: [S-###]
      constraints:
        - (ログ保持、変更管理、データ所在、暗号化、分離、など)
- data_classification:
  - - data_type: <PII|payment|health|trade_secret|credentials|logs|other>
      sensitivity: high|medium|low
      storage_locations: (systems/regions) validated: yes|no|unknown
      sources: [S-###]
- trust_boundaries_and_third_parties:
  - - boundary: <internal/external/partner/vendor>
      connectivity: <vpn|directconnect|api|sftp|email|other>
      validated: yes|no|unknown
      sources: [S-###]
- identity_and_access_model:
  - idp: <Entra/Okta/ADFS/...> validated: yes|no|unknown sources:[S-###]
  - mfa_policy: <...> validated: yes|no|unknown sources:[S-###]
  - privileged_access: <PAM/JIT/Breakglass> validated: yes|no|unknown sources:[S-###]
- cloud_and_saas_footprint:
  - - provider: <AWS/Azure/GCP/SaaS>
      accounts_tenants: <...> validated: yes|no|unknown sources:[S-###]
      logging_status: <enabled/partial/unknown> sources:[S-###]
- operational_constraints:
  - testing_windows: <...> validated: yes|no|unknown sources:[S-###]
  - change_freeze_periods: <...> validated: yes|no|unknown sources:[S-###]
  - incident_response_process: <...> validated: yes|no|unknown sources:[S-###]
~~~~

---

## 6. Business Flow Cards（業務フロー単位の分解：推奨）
> “攻撃者が狙う目的” を業務フローで定義し、12/13のマトリクスとチェーンに接続する。

~~~~
Business Flow Card: BF-###
- name: <e.g., Payment Authorization / Account Opening / Claims Processing>
- validated: yes|no|unknown
- sources: [S-###]
- steps:
  - - step: 1
      actor: <customer|employee|system|partner>
      system: <app/service>
      trust_boundary: <internal/external/partner>
      data: <data_type>
      notes: <...>
- crown_jewel_touchpoints:
  - (どのステップが重要資産に触るか)
- security_assumptions_to_verify:
  - (unknownを列挙し、REQ化する)
- pentest_implications:
  - (狙うべき攻撃面、禁止事項、検証優先度)
~~~~

---

## 7. ドメイン→STEP1〜5 への接続ルール（固定）
### 7-1. STEP1（ASM/Recon）へ
- TRUST/ARCH の Claim を Recon の探索範囲・優先度に反映
- 例：第三者接続（BF/Trust）→ 外部資産・サプライチェーン面の優先度上げ

### 7-2. STEP2（Auth/IAM/SSO/MFA）へ
- ID/REG の Claim を認証試験の重点に反映
- 例：高権限運用（PAM/JIT）unknown → REQ-ACC/REQ-ARC を先に解消

### 7-3. STEP3（AD/内部ID/横展開）へ
- ARCH/OPS の Claim を“横展開が成立する現実条件”として固定
- 例：セグメント分離 unknown → 観測点（08）と併せて REQ-ARC/REQ-LOG に落とす

### 7-4. STEP4（Cloud/Hybrid）へ
- Cloud/SaaS の logging_status・権限モデルを確定してからチェーンを作る
- 監査が unknown のまま Hop5/7 を論じない（unknownとして要求化）

### 7-5. STEP5（PT/TLPT）へ
- BIZ/AVAIL/REG により「許されるTTP」「Impactの語り方」「報告会の意思決定軸」を固定
- 08_blue-dfir の P0/P1/P2 は、Domain Claim（REG/ID/OPS）を根拠にする

---

## 8. REW/CC（STEP5-14）へ落とすための “質問リスト”（最小）
> unknownを減らすための “最初の質問セット”。回答は Source（S-###）として登録する。

- REG：
  - 監査対象（どの規格/法令/社内監査）と、必須ログ保持期間は？
  - 提供不可ログの理由（契約/法務/技術）は何か？
- AVAIL：
  - 停止不可のシステムはどれか？テスト可能な時間窓は？
- ID：
  - IdP、MFA、条件付きアクセス、特権運用（PAM/JIT）の現状は？
- CLOUD：
  - 監査ログ（操作/アクセス）は有効か？どこに出るか？保持は？
- TRUST：
  - 第三者接続（VPN/専用線/API/SFTP）と責任分界は？
- OPS：
  - 変更管理のフロー、インシデント対応の窓口、ログ抽出担当は？

---

## 9. 次のファイル（BASICS09-02）への接続
次は、業界ごとの “標準的な攻撃面と制約” をパターン化し、12_attack-matrices/13_chains に直接流し込める形へする。

- 次：keda-lab/09_industry-domain/02_industry-patterns-library.md
  - 業界別（finance/healthcare/retail/…）の Crown Jewels / 典型フロー
  - 業界別の優先TTP（MITRE/PTES/ASVS/WSTGとの接続点）
  - 規制・停止制約・第三者接続の “頻出パターン” と REW/CC要求テンプレ
<<<END>>>
