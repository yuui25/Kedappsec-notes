<<<BEGIN>>>
# keda-lab Security Project 統合指示（01 ASM/Recon → 05 PT/TLPT を幹とし、06 以降で全知識を補完する地続き体系ルール）

本指示は、`keda-lab` プロジェクト内のすべてのチャット・すべてのファイル生成に共通して適用する  
「統合ルール」「出力ルール」「ステップ設計ルール」を定めるものである。  

Recon（外部資産調査）から始まり、認証・AD・クラウドを経て、最終的に PT / TLPT に到達するまでを  
**知識が途切れず積み上がるように設計すること** を目的とする。  

そのうえで、01〜05 を「攻撃チェーン本編」、06〜13 を「本編を支える基礎・整理・ラボ」として位置づけ、  
WebPT / PT / TLPT に必要な全領域を網羅する。

---

## 1. フォルダ体系（固定）

本プロジェクトのフォルダ構成は以下に固定する。

~~~~
keda-lab/
│
├── 00_meta/               # 共通ルール・用語集・テンプレ・全体設計
│
├── 01_asm-recon/          # 外部資産・ASM・Recon（STEP1群：Initial Discovery）
├── 02_auth-iam-sso-mfa/   # 認証・IAM・SSO・MFA（STEP2群：Authentication）
├── 03_ad-kerberos-lateral/# AD・Kerberos・横展開・内部 ID 基盤（STEP3群：Internal ID & Lateral）
├── 04_cloud/              # クラウド（AWS/Azure/GCP）構成と攻撃面（STEP4群：Cloud）
├── 05_pentest-chain/      # 攻撃チェーン・PT・TLPT シナリオ（STEP5群：Attack Chain）
│
├── 06_os-network-protocol/# OS / Network / Protocol 基礎（PT/TLPT の土台）
├── 07_web-application-deep/# Web アプリ内部構造・技術スタック深掘り（OSWE 領域）
├── 08_blue-dfir/          # Blue Team / DFIR / Detection & Response
├── 09_industry-domain/    # 金融・医療・製造など業界特化ドメイン知識
├── 10_social-physical/    # ソーシャルエンジニアリング・物理侵入・運用面
├── 11_tools-lab/          # ツール・ラボ構築・演習環境
├── 12_attack-matrices/    # 攻撃マトリクス・三軸整理・知識体系化
└── 13_chains/             # 攻撃チェーンテンプレ・シナリオ集
~~~~

すべてのチャット・すべての出力は、  
必ずこのフォルダ体系を前提としてファイルを配置・更新する。

01〜05 は「Recon → Auth → AD/内部 ID → Cloud → PT/TLPT」という攻撃者視点の本編の流れ。  
06〜13 は、その本編で現れる技術・シナリオを支える **基礎解説・ラボ・整理用フォルダ** である。

---

## 2. 出力フォーマットルール（全ファイル共通）

1. すべての出力は **GitHub にそのまま貼れる Markdown** とする。  
2. 出力メッセージは **1つのコードブロックのみ** とし、構造は以下に固定する。  
   - 先頭：` ```md `  
   - 本文：`<<<BEGIN>>>` 〜 `<<<END>>>`  
   - 末尾：` ``` `  
3. 本文中でコードブロックを使う場合は **必ず `~~~~`** を用い、  
   **絶対に ` ``` ` を使用しない。**  
4. ファイル生成時は必ず、冒頭で **「対象フォルダ」と「ファイル名」と「STEP名」** を明示する。  
   （例：`フォルダ: 01_asm-recon/`, `ファイル名: 03_osint_initial_discovery.md`, `STEP1-2 OSINT 初期発見` など）

---

## 3. ステップ連結ルール（「地続き」の最重要ルール）

すべての STEP は、**必ず前 STEP の成果物を使う** ことを強制する。  
ステップを単体で完結させてはならず、必ず「以前のステップがあるからこそ出来る分析」を含める。

### 3.1 前ステップとの関係セクションを必須化

各ファイルには、以下のようなセクションを必須とする。

- 「前ステップとの関係」  
  - どの STEP（例：STEP1-3, STEP1-5, STEP3-2 など）の成果を使うか  
  - その成果がこの STEP のどの部分で活かされるか  
  - 具体的にどの情報（例：サブドメイン一覧 / 技術スタック / クラウド資産情報 / ID 連携情報 等）を参照するか  

例：  
- 「STEP1-3 で列挙したサブドメインのうち、ログイン画面を持つ URL 群を本 STEP の認証方式解析に利用する」  
- 「STEP1-5 で確認した CloudFront Origin 情報を、API の入口判定とアクセス経路の分析に使用する」  
- 「STEP2-3 で整理した OIDC クライアント情報を、本 STEP3-7 の AD / Kerberos / SSO 関係整理に利用する」  

### 3.2 「前の情報を使わない STEP」は禁止

- 新しい STEP を書く際に、前 STEP の情報を使わずに完結する構成は禁止。  
- 各 STEP は必ず「何らかの前 STEP の成果物に依存していること」を明記する。  

### 3.3 知識の積み上げ順を固定（01〜05 の本編）

- STEP1（ASM/Recon）で集めた外部資産情報を、  
  STEP2（Auth/IAM/SSO/MFA）の調査対象 URL・サービス選定に必ず利用する。  
- STEP2 で判明した認証方式・セッション管理の情報を、  
  STEP3（AD/Kerberos/Lateral）で「内部認証・横展開がどう接続しうるか」の前提として利用する。  
- STEP3 で得た AD・Kerberos・横移動の理解を、  
  STEP4（Cloud）で「クラウド＋オンプレ混在環境におけるアイデンティティ連携」の分析に利用する。  
- STEP1〜4 で得たすべての情報を、  
  STEP5（Pentest-Chain/TLPT）での攻撃シナリオ設計・チェーン構築に利用する。  

### 3.4 06〜13 フォルダの役割（本編を支える補助フォルダ）

- 06_os-network-protocol 〜 13_chains の各 STEP は、  
  **01〜05 で出てきた技術やシナリオを理解するための基礎・整理・演習** を担う。  
- 各ファイルは、「どの本編 STEP（01〜05）の理解を補うための内容か」を必ず明記する。  
  - 例：「本 STEP6-2 は STEP3-4（SMB / LDAP / DNS / RDP の役割整理）を理解するための TCP/IP / Port 基礎」  
  - 例：「本 STEP7-3 は STEP5-4（技術スタック別攻撃面マッピング）の『Laravel × MySQL』部分を具体化するための FW 内部構造整理」  

---

## 4. 各フォルダ内のステップ設計（できるだけ細かく区切る）

各フォルダ（01〜05 の本編＋06〜13 の補助フォルダ）は、  
可能な限り細分化された STEP 群として扱う。  
以下は推奨のステップ分割例であり、新しいファイルを作る際はこの設計を前提とする。

---

### 4.1 01_asm-recon/（STEP1群：外部資産・Recon）

例：

- STEP1-1：外部公開資産の定義と分類  
- STEP1-2：OSINT 初期発見（DNS / CT / ASN / Scan 情報源）  
- STEP1-3：サブドメイン列挙（Passive / Active / Permutation）  
- STEP1-4：技術スタック推定（ヘッダ / HTML / JS / WAF / CDN）  
- STEP1-5：クラウド露出資産（S3 / Blob / GCS / CDN / WebApps）  
- STEP1-6：公開ソースコード・SaaS・CI/CD 露出  
- STEP1-7：発見資産のリスク優先度付け  
- STEP1-8：STEP1 総まとめ（STEP2 への橋渡し）

以降のフォルダも同様に、細かい STEP 番号で分割する。

---

### 4.2 02_auth-iam-sso-mfa/（STEP2群：認証・IAM・SSO・MFA）

例：

- STEP2-1：Web 認証方式の全体像（Cookie/Session, Token, OAuth, SAML など）  
- STEP2-2：Session/Cookie ベース認証とその観察ポイント  
- STEP2-3：OAuth2 / OIDC のフローと、STEP1 で見つけた URL への適用  
- STEP2-4：SAML / SSO（IdP / SP）の構造と挙動分析  
- STEP2-5：MFA 手法とフローの分類（TOTP / Push / SMS など）  
- STEP2-6：セッション管理・ログアウト・Remember-me などの挙動確認  
- STEP2-7：認証まわりの典型的な弱点（高リスク抽出のみ、攻撃詳細は後工程）  
- STEP2-8：STEP1 の外部資産情報と STEP2 の認証情報の統合整理（STEP3 への橋渡し）

すべての STEP2 ファイルは、  
「STEP1 で得た URL 群・ドメイン・クラウド資産情報を前提に、どの認証方式がどこで使われているか」を明示する。

---

### 4.3 03_ad-kerberos-lateral/（STEP3群：AD・Kerberos・横展開・内部 ID 基盤）

例（拡張版）：

- STEP3-1：外部資産（STEP1）・認証方式（STEP2）から推定する AD / 内部 ID 構造の初期モデル化  
- STEP3-2：AD ドメイン基本構成（OU / DC / GPO / ACL）の体系理解  
- STEP3-3：Kerberos Deep Dive（AS-REQ/REP、TGS-REQ/REP、PAC）とチケット保存場所（LSASS / DPAPI）の理解  
- STEP3-4：内部サービス（SMB / LDAP / DNS / RDP）の役割整理と攻撃面の分析  
- STEP3-5：Credential & Token Handling（LSASS / Mimikatz / DPAPI / Token）の概念整理  
- STEP3-6：横展開（Lateral Movement）パターンの体系整理（SMB / WMI / WinRM / RDP / SSH / Socks5）  
- STEP3-7：STEP2 の認証方式（OIDC / SAML / SSO）と AD / Kerberos の関係（フェデレーション / ハイブリッド構成）  
- STEP3-8：Hybrid Identity（AD Connect / Entra ID）の仕組みとリスクポイント整理  
- STEP3-9：STEP1〜3 の情報を統合した内部ネットワーク / 社内構造の見取り図作成（STEP4 への橋渡し）

ここでは STEP1 / STEP2 で得た「外部公開資産と認証方式情報」を前提として  
裏側に存在する AD・内部 ID 基盤の構造を推定し、横展開の下地を作る。

---

### 4.4 04_cloud/（STEP4群：クラウド構成と攻撃面）

例（拡張版）：

- STEP4-1：クラウド全体構成（IaaS/PaaS/SaaS）と IAM（AWS / Azure / GCP）の基本整理  
- STEP4-2：STEP1 で見つけたクラウド露出資産を IAM 構造へマッピングする（API / Storage / WebApps）  
- STEP4-3：API Gateway / Lambda / Functions / WebApps の観察ポイント（認証 / ID 連携）  
- STEP4-4：クラウドネットワーク構成（VPC / Subnet / RouteTable / SecurityGroup）の概念整理  
- STEP4-5：クラウドにおける ID 連携（STEP3 で整理した AD / Entra ID / SSO との関係）  
- STEP4-6：クラウド特有の誤設定（IAM / Storage / Serverless / Network）の分類（詳細攻撃は STEP5 側で扱う）  
- STEP4-7：オンプレ＋クラウド混在時の攻撃面マップ作成（AD → Cloud → Application）  
- STEP4-8：Cloud Privilege Escalation（権限昇格パターン）の整理（攻撃詳細は STEP5）  
- STEP4-9：STEP1〜4 の全情報を統合した「技術構造ビュー」作成（STEP5 への橋渡し）

各 STEP4 は、  
STEP1（クラウド露出）、STEP2（認証/SSO）、STEP3（ID/AD/Hybrid）で得た情報を起点に  
クラウド環境がどのように統合され攻撃面を形成するかを整理する。

---

### 4.5 05_pentest-chain/（STEP5群：攻撃チェーン・PT・TLPT）

例（拡張版）：

- STEP5-1：攻撃チェーン全体像の再整理（Recon → Initial Access → Lateral → Cloud → Impact）  
- STEP5-2：STEP1〜4 の情報を統合し、攻撃シナリオ候補を体系的に生成する方法  
- STEP5-3：Web ペネトレーションテストの標準フロー整理（Recon / Auth / Session / App / API）  
- STEP5-4：技術スタック別攻撃面マッピング（言語 / FW / 認証方式 / インフラ）  
- STEP5-5：AD × Cloud × Web を統合したハイブリッド攻撃チェーンの設計  
- STEP5-6：TLPT シナリオ設計（インフラ＋アプリ＋認証＋クラウド＋検知モデル）  
- STEP5-7：MITRE ATT&CK を用いた攻撃チェーンの技術的説明と意図の明確化  
- STEP5-8：検知・ロギング観点（Blue / DFIR）との橋渡し（攻撃 → 検知 → 対応）  
- STEP5-9：STEP1〜5 を通じた「セキュリティエンジニアとしての能力要件」の整理  
- STEP5-10：総まとめ（今後の拡張：Mobile / API 特化 / Industry 特化）

STEP5 では、**必ず STEP1〜4 の成果物を前提に**  
各情報が攻撃フェーズ（Initial / PrivEsc / Lateral / Cloud / Impact）に  
どのように紐づくかを明示してシナリオを構築する。

---

### 4.6 06_os-network-protocol/（基礎群：OS / Network / Protocol）

例：

- STEP6-1：TCP/IP・Port・Routing 基礎（STEP1 のポートスキャン / STEP3-4 の内部サービス理解を補完）  
- STEP6-2：Windows OS 基礎（プロセス / サービス / ログ）― STEP3-5・STEP5-2 での操作前提  
- STEP6-3：Linux OS 基礎（systemd / permission / capabilities）― Web サーバ / 攻撃用 VM 前提  
- STEP6-4：VPN / VDI / Zero Trust の通信モデル（STEP5-1 初期侵入での前提）  
- STEP6-5：SMB / LDAP / DNS / DHCP プロトコル詳細（STEP3-4 / STEP3-6 の補足）  
- STEP6-6：TLS / HTTP1.1・2・3 / Proxy の詳細（STEP1-4 / STEP2-1〜2 / STEP5-3 の補足）  

ここでは、01〜05 で暗黙の前提となる OS / Network / Protocol の基礎を  
**「どの STEP で使うための知識か」を明示したうえで整理する。**

---

### 4.7 07_web-application-deep/（基礎群：Web アプリ内部構造・技術スタック）

例：

- STEP7-1：PHP / Laravel のリクエスト〜レスポンス内部処理（STEP5-4 の Laravel チェーン補完）  
- STEP7-2：Java / Spring の DI / MVC / Filter / Interceptor 理解（STEP5-4 の Java チェーン補完）  
- STEP7-3：ASP.NET / C# の Pipeline・ViewState の仕組み（既存 ViewState 事例との接続）  
- STEP7-4：Node.js / Express の Middleware / 非同期処理（API 攻撃チェーンの前提）  
- STEP7-5：DB / ORM（SQL / NoSQL）の内部処理（STEP5-4 の SQLi / ORM bypass の前提）  
- STEP7-6：GraphQL / gRPC の構造とスキーマ設計（ASM/Recon・WebPT 双方への橋渡し）  

ここでは、STEP5 で使う「技術スタック別攻撃面マッピング」を  
実際の言語 / FW の内部挙動で裏付ける。

---

### 4.8 08_blue-dfir/（基礎群：Blue / DFIR / Detection & Response）

例：

- STEP8-1：Windows Event Log / Sysmon の基本と、STEP5-2〜5 の攻撃に対する検知ポイント  
- STEP8-2：EDR の一般的な動作モデルと回避ではなく「どう見えるか」の整理  
- STEP8-3：SIEM（Sentinel / Splunk）のルールと TLPT の報告観点  
- STEP8-4：Cloud Logs（CloudTrail / Azure Activity / GCP Audit）と STEP4 / STEP5 の関連付け  
- STEP8-5：インシデントライフサイクル（検知 → 封じ込め → 根絶 → 復旧）  

TLPT シナリオ（STEP5-6〜8）で「攻撃したらどう検知されるか」を説明するための基礎を整理する。

---

### 4.9 09_industry-domain/（基礎群：業界特化ドメイン）

例：

- STEP9-1：金融系システムのレイヤ構造（勘定系 / 情報系 / SWIFT / SSO 基盤）  
- STEP9-2：金融系 Web / API の典型アーキテクチャ（STEP5-6 金融 TLPT シナリオの前提）  
- STEP9-3：医療系（電子カルテ / HL7）における ID / NW 構造の概要  
- STEP9-4：製造（OT / ICS）のネットワーク分離と監視モデル概要  

ここでは、STEP5-6（TLPT シナリオ設計）で対象業界を具体的に描くための最低限のドメイン知識を整理する。

---

### 4.10 10_social-physical/（基礎群：ソーシャル・物理・オペレーション）

例：

- STEP10-1：OSINT（個人 / 組織 / 業務情報）の深掘りと STEP1〜2 への接続  
- STEP10-2：ソーシャルエンジニアリングの典型パターンとメール / 電話シナリオ  
- STEP10-3：物理侵入の基本概念と TLPT スコープとの関係  
- STEP10-4：現場オペレーション（標的型メール送信フロー等）の整理  

ここでは、主に TLPT における「技術以外の初期侵入・展開」を理解する。

---

### 4.11 11_tools-lab/（基礎群：ツール・ラボ・演習環境）

例：

- STEP11-1：攻撃者ラボ環境（Kali / Windows / AD ラボ）の全体設計  
- STEP11-2：クラウドラボ（AWS / Azure / GCP）環境の最小構成と攻撃チェーンとの対応  
- STEP11-3：Burp / mitmproxy / VEX / 独自ツールの役割整理  
- STEP11-4：BloodHound / Covenant / Neo4j など AD / Lateral 用ツールの利用前提整理  

ここでは、01〜05 で登場する「手の動き」を実際に再現するための環境とツールを整理する。

---

### 4.12 12_attack-matrices/（整理群：攻撃マトリクス・三軸整理）

例：

- STEP12-1：Web 機能 × 脆弱性 × 影響度のマトリクス化（既存 kedappsec-notes と連携）  
- STEP12-2：技術スタック × 脆弱性 × 攻撃チェーン段階のマトリクス化  
- STEP12-3：Recon → Auth → AD → Cloud → PT の各フェーズにおける情報項目一覧化  
- STEP12-4：MITRE ATT&CK との対応付け表作成  

ここでは、01〜11 で得た知識を「俯瞰して見える形」に整理する。

---

### 4.13 13_chains/（整理群：攻撃チェーンテンプレ・シナリオ集）

例：

- STEP13-1：WebPT 標準攻撃チェーンテンプレ（環境非依存版）  
- STEP13-2：AD 侵攻チェーンテンプレ（AS-REP / Kerberoasting / Lateral）  
- STEP13-3：Cloud 侵攻チェーンテンプレ（IAM Misconfig → PrivEsc → Data Access）  
- STEP13-4：金融系 TLPT シナリオテンプレ（VPN / SSO / SWIFT / SharePoint）  
- STEP13-5：OSWE 的「脆弱性深堀り」チェーン（ソースコードあり前提）  

ここでは、STEP5 で構築した攻撃チェーンを「再利用可能なテンプレート」として再構成する。

---

## 5. 各ファイルの標準セクション構成

すべての STEP ファイルは、原則として以下のセクションを持つ。

1. 目的  
2. 前ステップとの関係  
3. 情報源・手法の体系  
4. 実践手順（観察・確認内容）  
5. 注意事項  
6. 参考文献  

これにより、  
- 何のための STEP か  
- どの STEP とつながるか  
- どのように手を動かすか  
が常に明確になる。

---

## 6. 会話ルール（どのチャットでも共通）

- ユーザーが「次」と述べた場合、現在のフォルダ・STEP の流れに沿った「次の粒度の STEP」を提案・生成する。  
- 新しいチャットを開始した場合でも、この統合指示に基づいて  
  `keda-lab` プロジェクトの連続性を維持する。  
- 曖昧な依頼の場合、「このプロジェクトのどのフォルダ／STEPに属する話か」を ChatGPT 側で判断し、  
  可能な限り既存の STEP 体系に統合する形で回答する。  

---

## 7. 最重要原則（繰り返し）

- ステップは常に地続きであり、**前のステップの情報が必ず次で生きるように書く**。  
- Recon（ASM）から始まるすべての情報は、最終的に PT / TLPT でのシナリオ設計に繋がるように使う。  
- 06〜13 のフォルダは、01〜05 の本編で登場する技術・概念・シナリオを  
  いつでも「深掘り・検証・再構成」できるようにするための土台である。  
- 大量の情報は「順番」と「関連づけ」で整理する。この順番（01→02→03→04→05）を幹として崩さない。

以上を、本プロジェクトの統合指示として、  
すべてのチャット・すべてのファイル出力に適用する。

<<<END>>>