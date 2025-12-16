# 20_code-search_公開リポジトリ横断（keywords_secrets_責任分界）.md

## ガイドライン対応（ASVS / WSTG / PTES / MITRE ATT&CK）
- ASVS：
  - V1（アーキテクチャ/境界）：公開コード（Org/Repo/依存）を「第三者から見える境界情報」として扱い、資産境界（公式/非公式・委託/外注）を確定する。
  - V14（運用/構成）：Secrets/設定断片が“どこから漏れるか”を経路として整理し、修正主体（コード/CI/Secrets管理）へ落とす。
- WSTG：
  - Information Gathering：公開情報（コード/設定/ドキュメント）から入口・環境名・外部連携を抽出し、Web recon へ接続。
  - Configuration and Deployment：CI設定・環境変数参照・ログ出力など、運用起因の露出を観測する。
- PTES：
  - Intelligence Gathering（公開情報収集）→ Threat Modeling（漏えい経路/境界の仮説）→ Vulnerability Analysis（運用ズレの疑い）へ接続。
- MITRE ATT&CK：
  - Reconnaissance：公開リポジトリ/コード検索による情報収集（Discovery）として位置づける。目的は侵入手順ではなく「露出面の棚卸しと境界確定」。

## 目的（この技術で到達する状態）
- 公開リポジトリ（GitHub等）から、**入口（URL/ホスト/パス）**・**環境境界（dev/stg/prod）**・**外部依存（SaaS/IdP/クラウド）**・**Secrets漏えい経路（出力/参照/保存）** を抽出し、優先度付きで次工程へ渡せる。
- “値を盗む”ではなく、**漏えいし得る経路（どこに出るか）** を状態化し、06_config（Secrets）・04_saas（監査）へ接続できる。
- 公式/非公式、組織/個人、委託/外注の可能性を強/中/弱で整理し、スコープ逸脱を防げる。

## 前提（対象・範囲・想定）
- 対象：
  - 公式Org/Repo候補（19_github_* で作った候補）
  - そこに紐づくPages/Docs、.github/workflows 等の運用断片
  - 依存（公開パッケージ/テンプレ）由来の設定断片（ただし混入に注意）
- 想定：
  - 公開コードに“本物の秘密”があるとは限らない。一方で、**環境名・URL・連携先**は高頻度で残る。
  - 文字列ヒットは誤検知が多い。重要なのは「境界として意味があるか（資産/信頼/権限）」で選別すること。
- 禁止（運用境界）：
  - スコープ外の組織・個人に対する探索の拡大
  - 発見した値を用いた不正利用（このファイルは“露出面の特定と是正”が目的）

## 観測ポイント（何を見ているか：プロトコル/データ/境界）
### 1) 検索単位は「候補資産（Org/Repo/Pages）」で固定する
- まずは候補を固定：
  - 公式らしさ（強/中/弱）
  - 主要ドメイン（10_ctlog/02_tls と整合）
  - 事業/プロダクト名（README/Docsの整合）
- その上で “横断検索” を行う（いきなり全世界を探さない）。

### 2) 抽出対象は4カテゴリに限定する（増やしすぎない）
- 入口（Web/API）：
  - `https://...`、`/api`、`/graphql`、`/admin`、`/swagger`、`/openapi`
- 環境境界：
  - `dev` `stg` `prod` `internal`、リージョン/クラスタ名、サブドメイン規則
- 外部依存（信頼境界）：
  - IdP/SSO、監視、CI/CD、クラウド、決済、メール等の連携先断片
- Secrets“経路”（値ではなく経路）：
  - `secrets.*` 参照、環境変数名、ログ出力設定、設定ファイルに置く設計の痕跡

### 3) 「強度（強/中/弱）」で状態化する（断定しない）
- 強：
  - 対象ドメイン/証明書（SAN）と整合し、入口URL/環境名が複数箇所で一致する
- 中：
  - 一部一致（プロダクト名/URL断片）だが公式根拠が弱い、または委託先Repoの可能性
- 弱：
  - 類似名/フォーク/個人メモの可能性が高い（候補止まりで退避）

### 4) 境界（資産/信頼/権限）に落とす
- 資産境界：
  - “公式らしさ”と“対象ドメイン整合”で、対象資産に紐づくかを整理する
- 信頼境界：
  - 外部依存（SaaS/IdP/クラウド）を列挙し、04_saas/01_idp・02_saasへ繋ぐ
- 権限境界：
  - Secrets/CI/設定は権限境界の中心。公開に出る“経路”があるなら、06_config_02 へ渡して是正対象を特定する

## 結果の意味（その出力が示す状態：何が言える/言えない）
- 言えること（状態）：
  - 公開リポジトリ由来で「入口」「環境境界」「外部依存」「Secrets経路」が観測できる
  - それが対象資産に紐づく可能性（強/中/弱）と根拠
  - 次に見るべき場所（Web入口、SaaS連携、Secrets運用）が優先度付きで決まる
- 言えないこと（断定しない）：
  - 文字列が“本番で有効”である断定
  - 見つかった断片が“脆弱性そのもの”である断定（ここは入口と境界の確定）

## 攻撃者視点での利用（意思決定：優先度・攻め筋・次の仮説）
- 優先度が上がる状態：
  - 対象ドメインに直結する入口（管理/認証/コールバック/Swagger等）が見える
  - dev/stg の存在や命名規則が見える（環境境界が増える）
  - 外部依存（IdP/CI/クラウド）への連携断片が多い（信頼境界が複雑）
  - Secretsの“出力経路”が見える（ログ/設定/CIで漏れ得る）
- 次の仮説：
  - 仮説A：入口情報は正しいが到達性が不明
    - 次：03_http・12_waf_cdnで到達性と外周成立点を観測し、yes/no/unknown化
  - 仮説B：Repoは委託/外注で、対象境界が揺れる
    - 次：17_rdap_whois と突合し、修正主体/責任分界を整理して深掘りを制御
  - 仮説C：Secretsの“経路”が濃い
    - 次：06_config_02 へ“経路”として渡し、値追跡ではなく運用是正へ落とす

## 次に試すこと（仮説A/Bの分岐と検証）
- 仮説A（到達性が不明）：
  - 次の一手：
    - 抽出URLを少数の代表点に絞り、03_httpで挙動（200/30x/401/403）を観測
    - unknown（403/チャレンジ）は12_waf_cdnで原因分類してから再評価
  - 到達点：
    - “公開情報→現状入口”の接続ができ、Web reconの精度が上がる

- 仮説B（境界が揺れる）：
  - 次の一手：
    - 公式根拠（公式サイトリンク、対象ドメインのCNAME/CT整合）で強度を上げる
    - 上がらない候補は「候補止まり」で退避し、スコープ逸脱を防ぐ
  - 到達点：
    - 対象内/対象外の混入を抑え、調査の一貫性が保てる

- 仮説C（Secrets経路が濃い）：
  - 次の一手：
    - “変数名/参照/出力”だけを抽出し、06_config_02へ渡す（値は扱わない）
    - 04_saas の観点で、GitHub側の監査（権限/ログ/トークン運用）へ接続する
  - 到達点：
    - 「漏えいの事後対応」ではなく「漏えい経路の設計是正」に落とせる

## 手を動かす検証（Labs連動：観測点を明確に）
- 関連 labs：
  - `04_labs/01_local/01_attack-box_作業端末設計.md`（証跡保存・差分管理）
- 取得する証跡（目的ベースで最小限）：
  - `code_search_inventory.csv`：source(org/repo), confidence(強/中/弱), evidence
  - `code_search_extracted.csv`：category(entry/env/third-party/secret_path), value, source_ref
  - 抽出した入口URLの現状観測結果（03_http形式で最小限）

## コマンド/リクエスト例（例示は最小限・意味の説明が主）
~~~~
# 目的：対象と確定したリポジトリ群（ローカル複製）から、入口/環境境界/外部依存/Secrets経路を抽出する
# 注：値の抽出ではなく“経路”の抽出に限定する

# (1) 入口候補（URL/API/管理導線）
rg -n --hidden --no-ignore-vcs "https?://|/api/|/graphql\b|/admin\b|/swagger\b|/openapi\b" <repo_dir> | head -n 50

# (2) 環境境界（dev/stg/prod/internal）
rg -n --hidden --no-ignore-vcs "\b(dev|stg|stage|prod|production|internal)\b" <repo_dir> | head -n 50

# (3) 外部依存（IdP/クラウド/監視など“境界”になりやすい語だけ）
rg -n --hidden --no-ignore-vcs "OIDC|SAML|oauth|issuer|AWS_|AZURE_|GCP_|Sentry|Datadog" <repo_dir> | head -n 50

# (4) Secrets“経路”（参照/出力の匂い：値は追わない）
rg -n --hidden --no-ignore-vcs "secrets\.|process\.env|ENV\[|set -x|printenv|echo \\$" <repo_dir>/.github/workflows <repo_dir> 2>/dev/null | head -n 50
~~~~
- 観測していること：
  - 公開コードが示す「入口」と「境界（環境・外部連携・Secrets経路）」の棚卸し

## 参考（必要最小限）
- `01_topics/01_asm-osint/19_github_actions_pages_露出面（org_repo_pages）.md`
- `01_topics/01_asm-osint/17_rdap_whois_組織境界（登録者_委託_関連会社）.md`
- `01_topics/01_asm-osint/03_http_観測（ヘッダ・挙動）と意味.md`
- `01_topics/02_web/06_config_02_Secrets管理と漏えい経路（JS_ログ_設定_クラウド）.md`
- `01_topics/04_saas/02_saas_共有・外部連携・監査ログの勘所.md`

## リポジトリ内リンク（最大3つまで）
- 関連 topics：`01_topics/02_web/06_config_02_Secrets管理と漏えい経路（JS_ログ_設定_クラウド）.md`
- 関連 playbooks：`02_playbooks/01_asm_passive-recon_資産境界→優先度付け.md`
- 関連 labs：`04_labs/01_local/01_attack-box_作業端末設計.md`