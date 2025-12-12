<<<BEGIN>>>
# 09_industry-domain / BASICS09-02 業界パターンライブラリ（Crown Jewels/典型フロー/優先TTP/制約→REQ化）

## 1. 目的（本編STEPへの地続き）
本ファイルは、BASICS09-01 の Domain Claim（DC-###）を作る際に
「業界で頻出する重要資産・業務フロー・制約・攻撃面」を参照できる **パターン集** を提供する。

- “一般論” をそのまま結論にしない（必ず DC-### に落として yes/no/unknown＋S-### で裏取り）
- ただし、初期仮説（どこを優先して確認すべきか）としては強力に使う
- 12_attack-matrices（業界別TTP選定）と 13_chains（業界別Chain Card）へ直結させる

接続先：
- STEP1（01_asm-recon）：業界特有の外部資産・第三者接続の探索優先度
- STEP2（02_auth-iam-sso-mfa）：認証基盤/特権運用の重点
- STEP3（03_ad-kerberos-lateral）：横展開が成立しやすい内部条件の重点
- STEP4（04_cloud）：クラウド監査/権限変更（Hop5/7相当）の重点
- STEP5（05_pentest-chain）：Impact（事業影響）と制約（停止不可/法務）を根拠化
- 08_blue-dfir：P0（認証/権限/監査）優先の根拠付け
- 12_attack-matrices / 13_chains：業界別のマトリクスとチェーン生成

---

## 2. 運用ルール（固定）
### 2-1. Pattern → Claim 変換（必須）
このライブラリの各パターンは、必ず BASICS09-01 の Domain Claim に変換して使う。

- Pattern ID：P-IND-XXX
- 変換先：DC-###（validated: yes/no/unknown、sources: S-###）

### 2-2. Pattern は “確認優先度” を持つ
- P0：攻撃チェーン優先度に直結（認証/権限/監査/停止制約）
- P1：横展開/データアクセスに直結（内部接続/第三者/データ所在）
- P2：周辺（運用成熟度/例外プロセス）

### 2-3. REW/CC（STEP5-14）トリガを内蔵する
unknown を減らすため、パターンごとに “最小質問（REQ化ポイント）” を持つ。

---

## 3. パターン・カタログ（業界横断：共通パターン）
### P-IND-001（P0）：停止制約が強い（可用性最優先）
- 典型：24/365サービス、ピーク時間帯、バッチ窓のみ変更可
- 影響：
  - STEP5：DoS系/負荷系の禁止、テスト窓の事前合意が必須
  - 08：ログ抽出・監査設定変更も運用窓制約を受ける（unknownを増やしがち）
- 優先確認（DC化）：
  - テスト可能時間窓、凍結期間、停止不可対象
- REW/CCポイント：
  - REQ-OPS（テスト窓の合意/調整フロー）
  - REQ-RET（ログ保持が短い場合は緊急エクスポート）

### P-IND-002（P0）：認証基盤が事業の要（SSO/IdP集中）
- 典型：Entra/Okta/ADFS、条件付きアクセス、端末準拠（MDM）
- 影響：
  - STEP2：MFA迂回/セッション/トークンが攻撃の主戦場
  - STEP5：Hop2 が崩れると全チェーンが成立する
  - 08：P0（Hop2）としてログ/相関が最重要
- 優先確認：
  - IdP、MFAポリシー、例外（Break-glass）、外部アクセス制御
- REW/CCポイント：
  - REQ-LOG（sign-in + correlation_id）
  - REQ-ACC（抽出権限）
  - REQ-COR（correlation_id保持）

### P-IND-003（P0）：権限変更が“事故”の中心（Role/Consent/Policy）
- 典型：クラウド権限、SaaS管理、サービスプリンシパル、委任
- 影響：
  - STEP4/5：Hop5/7（権限/監査停止）の主張が最重要
  - 08：監査ログが無い/薄いと unknown が増える
- 優先確認：
  - 監査ログの有効化/保持、管理操作の監査対象、変更管理プロセス
- REW/CCポイント：
  - REQ-ARC（監査設定証跡）
  - REQ-LOG（role_assign/consent）
  - REQ-INT（SIEM連携）

### P-IND-004（P1）：第三者接続が多い（委託/パートナー）
- 典型：VPN/専用線/API/SFTP、BPO、MSP
- 影響：
  - STEP1：外部資産・委託先境界の探索が重要
  - STEP3：信頼境界を跨いだ横展開の現実性が上がる
  - STEP5：責任分界（誰がログを持つか）がDFIR/REQに直結
- 優先確認：
  - 接続方式、責任分界、ログ保有者、契約制約（提供可否）
- REW/CCポイント：
  - REQ-OPS（窓口/抽出担当）
  - REQ-ARC（責任分界図/契約抜粋）
  - REQ-LOG（第三者側ログの提供範囲）

### P-IND-005（P1）：データ所在が分散（SaaS/クラウド/オンプレ混在）
- 影響：
  - STEP4：ログがどこに出るか不明だと unknown が増える
  - STEP5：Crown Jewel の実体（どのDB/どのテナントか）を誤る
- 優先確認：
  - 重要データのシステム、リージョン、アクセス経路（API/ETL）
- REW/CCポイント：
  - REQ-ARC（構成図/データフロー）
  - REQ-LOG（データアクセス監査）

### P-IND-006（P2）：運用成熟度のばらつき（監視/変更管理）
- 影響：
  - 08：unknown が増える（抽出経路未確立、保持短い）
  - STEP5：報告書の改善提案が中心になる
- 優先確認：
  - 監視体制、変更管理、インシデント対応、ログ抽出担当
- REW/CCポイント：
  - REQ-OPS（手順/担当）
  - REQ-RET（保持）

---

## 4. 業界別パターン（ドメイン仮説：DC化前提）
> ここは “業界ラベル” で括るが、必ず BASICS09-01 の DC-### に落として検証する。

### 4-1. Finance（金融）
#### P-FIN-001（P0）：決済・送金・口座に直結する Crown Jewels
- Crown Jewels 例：決済承認API、送金バッチ、口座開設、本人確認、限度額/不正検知設定
- 典型フロー：
  - BF：Account Opening / KYC / Payment Authorization / Funds Transfer
- 優先TTP（例：方向性）
  - 認証突破（Hop2）、権限/委任悪用（Hop5）、データ/取引操作（Hop6）、監査回避（Hop7）
- 制約仮説：
  - 監査・証跡要件が厳しい、停止不可、第三者（決済/AML/本人確認）接続が多い
- REW/CC最小質問：
  - 監査対象（内部監査/外部監査）、ログ保持期間、変更凍結、第三者接続の責任分界

#### P-FIN-002（P1）：不正検知（Fraud）と“例外運用”が存在
- 典型：緊急解除、手動承認、バックオフィス例外、Break-glass
- 影響：
  - STEP2/5：例外運用が攻撃面になる（権限と監査が重要）
- REW/CC：
  - REQ-ARC（例外運用手順）
  - REQ-LOG（例外操作の監査ログ）

### 4-2. Healthcare（医療）
#### P-HC-001（P0）：患者情報（PHI）と可用性（診療継続）
- 典型：電子カルテ、検査システム、予約、院内ネットワーク
- 制約：停止不可、PII/PHIの取り扱い制約、端末多様
- REW/CC：
  - REQ-OPS（テスト窓）
  - REQ-LOG（アクセス監査）
  - REQ-ACC（PHIマスキング方針）

### 4-3. Retail / eCommerce（小売）
#### P-RET-001（P0）：アカウント乗っ取りと決済連携
- 典型：会員基盤、決済ゲートウェイ、クーポン/ポイント
- REW/CC：
  - REQ-LOG（サインイン/決済連携ログ）
  - REQ-DET（ATO検知ユースケース）

### 4-4. Manufacturing（製造）
#### P-MFG-001（P0）：IT/OT境界と第三者保守
- 典型：工場ネットワーク、ベンダ保守VPN、停止コスト極大
- REW/CC：
  - REQ-ARC（IT/OT境界図）
  - REQ-OPS（停止制約、窓）
  - REQ-LOG（VPN/リモート保守ログ）

### 4-5. Public（公共）
#### P-PUB-001（P0）：住民情報・行政手続・委託多
- 典型：住民情報、申請、委託先運用、監査
- REW/CC：
  - REQ-ARC（委託範囲/責任分界）
  - REQ-RET（保持）
  - REQ-LOG（行政手続の監査ログ）

---

## 5. Pattern → DC-### 変換テンプレ（固定）
> 本ライブラリを使うときは必ずこの形で DC を作る。

~~~~
Pattern to Domain Claim:
- pattern_id: P-IND-XXX | P-FIN-XXX | ...
- claim_id: DC-###
- statement: (対象組織で当該パターンが成立するか？の主張)
- validated: yes|no|unknown
- sources: [S-###] | []
- priority: P0|P1|P2
- implications:
  - step1_recon: ...
  - step2_auth: ...
  - step3_ad: ...
  - step4_cloud: ...
  - step5_chain: ...
  - dfir_priority: (P0/P1/P2の根拠)
- rew_cc_if_unknown:
  - request: (最小質問/必要資料)
  - owner: client|both
  - deadline_type: D1|D2|D3|D4
~~~~

---

## 6. 次のファイル（BASICS09-03）への接続
次は、業界パターンを “攻撃チェーンの優先度” に変換するためのルールを固定する。
（業界ドメイン → 12_attack-matrices の入力仕様）

- 次：keda-lab/09_industry-domain/03_domain-to-matrix-mapping.md
  - DC-###（validated）を入力に、優先TTPを選ぶ
  - ASVS/WSTG/PTES/ATT&CK へのマッピングの粒度
  - “停止制約・法務制約” をテスト計画へ落とす手順
  - unknown の扱い（REQ化してからチェーン確定）
<<<END>>>
