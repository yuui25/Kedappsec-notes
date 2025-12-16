# 19_github_actions_pages_露出面（org_repo_pages）.md

## ガイドライン対応（ASVS / WSTG / PTES / MITRE ATT&CK）
- ASVS：V1（アーキテクチャ/境界）・V14（運用/構成）を支える前提として、GitHub（Org/Repo/Pages/CI）由来の公開面を「第三者SaaS境界」として固定し、露出（Pages/Artifacts/Actionsログ等）を運用観測として扱う。
- WSTG：Information Gathering / Configuration and Deployment の前提として、公開リポジトリ・Pages・CIログが示す「入口（URL）」「設定断片」「Secrets漏えい経路」を探索面の一部として整理する（過剰な深掘りはしない）。
- PTES：Intelligence Gathering（公開情報収集）→ Threat Modeling（SaaS境界・漏えい経路の仮説）→ Vulnerability Analysis（設定/運用のズレの疑い）へ繋げる。
- MITRE ATT&CK：Reconnaissance（公開情報の収集）として位置づける。目的は侵入ではなく、公開面と責任分界（Org/Repo/Pages/CI）の整理。

## 目的（この技術で到達する状態）
- GitHub由来の露出面を **(1) 公開ページ（GitHub Pages）**、**(2) リポジトリ公開情報（コード/README/設定）**、**(3) CI由来（Actionsログ/Artifactsの扱い）** に分けて、境界として整理できる。
- そこから得られる “入口候補（URL/サブドメイン/環境名）” “外部依存” “Secrets漏えい経路の可能性” を抽出し、02_web（recon/config）と 04_saas（監査ログ/連携）へ渡せる。
- 17_rdap_whois（組織境界）と束ねて、「公式/非公式」「組織/個人」「委託/外注」の可能性を強/中/弱で状態化できる。

## 前提（対象・範囲・想定）
- 対象：
  - 対象企業/プロダクトに関連し得る GitHub Organization / Repository / Pages
  - 既に得ているキーワード（ブランド名、プロダクト名、主要ドメイン、チーム名）
- 想定される落とし穴：
  - リポジトリ名やOrg名は類似が多く、なりすまし/ファン/個人の可能性がある（断定しない）
  - Pagesは `*.github.io` だけでなく、カスタムドメインに結びつく（05_cloud/10_ctlogと突合が必要）
  - Actions/Artifactsは権限が必要なことが多い（公開で見える範囲は限定的）。ここでは“露出経路の棚卸し”まで。
- できること/やらないこと：
  - できる：公開情報の観測、入口候補と境界の整理、証跡化
  - やらない：非公開領域の突破、アカウント侵害、権限回避（本プロジェクト方針に反する）

## 観測ポイント（何を見ているか：プロトコル/データ/境界）
### 1) GitHub露出面を3レイヤで扱う（整理軸）
- レイヤA：Org/Repo（公開情報・コード・設定）
  - `.github/workflows/*`（CIの入口・環境変数名・クラウド連携の匂い）
  - `Dockerfile`, `k8s`, `terraform`, `helm`（構成断片、環境名）
  - `README`, `docs`（URL/エンドポイント/管理画面導線、運用手順）
- レイヤB：Pages（公開ホスティング）
  - `https://<org>.github.io/<repo>/`（標準）
  - カスタムドメイン（CNAME設定・独自ドメイン紐付け：10_ctlog/05_cloudと接続）
- レイヤC：CIログ/Artifacts（露出経路としての可能性）
  - 公開リポジトリのActionsログは閲覧可能範囲がある（環境差）
  - “ログに何が出ると危険か”を、06_config_02（Secrets）へ接続して扱う（断定しない）

### 2) 抽出対象（“攻撃面/境界”として意味があるもの）
- 入口候補（URL/ホスト/パス）
  - APIベースURL、管理画面URL、Swagger/OpenAPI、GraphQL、Webhook
- 環境境界
  - `dev/stg/prod`、`internal`、リージョン、クラスタ名、アカウントID（断片）
- 外部依存（第三者境界）
  - IdP/SSO、CI/CD、クラウド、監視、SaaS連携（04_saasへ）
- Secrets漏えい経路の“匂い”
  - “値”を探すのではなく、“出力され得る場所”を固定する（ログ/設定/例外）
  - 例：`AWS_ACCESS_KEY_ID` のような“変数名”、`secrets.*` の参照、`set -x` など

### 3) “公式らしさ”の強度（強/中/弱）で境界を作る
- 強：
  - Org/Repoが公式サイトや公式ドキュメントからリンクされている（外部証跡あり）
  - Pagesのカスタムドメインが対象ドメインに一致（10_ctlog/02_tlsと整合）
- 中：
  - プロダクト名/社員名/メール断片など整合があるが、公式リンクが弱い
- 弱：
  - 類似名・フォーク・個人メモ等。対象外混入の可能性が高い

### 4) 境界（資産/信頼/権限）に落とす
- 資産境界：
  - GitHub上の公開面（Pages/Repo）が、対象資産の一部か、周辺資産（個人/外注）かを強度で整理する
- 信頼境界：
  - GitHubは第三者SaaS境界。公開設定・権限・監査ログ（04_saas）と連動する
- 権限境界：
  - ActionsやSecretsは権限領域。公開から見える範囲は限定的だが、設定ミスの影響は大きい（06_config/Secretsに繋げる）

## 結果の意味（その出力が示す状態：何が言える/言えない）
- 言えること（状態）：
  - GitHub由来の公開面（Pages/Repo）が存在するか
  - そこから抽出できた入口候補、環境境界、外部依存、漏えい経路の可能性
  - “公式らしさ”の強度（強/中/弱）と根拠（リンク/ドメイン整合等）
- 言えないこと（断定しない）：
  - 非公開設定の内実（ActionsのSecrets値等）
  - “見つけた断片＝本番で有効”の断定（あくまで観測と仮説）

## 攻撃者視点での利用（意思決定：優先度・攻め筋・次の仮説）
- 優先度が上がる状態（診断として）
  - 公式らしさが強いGitHub資産があり、環境境界（dev/stg）や管理導線が露出している
  - Pagesが対象ドメインに紐づき、CDN/WAF外の別入口になっていそう（05_cloud/12_waf_cdnへ接続）
  - CI設定に外部連携（クラウド/IdP）が見える（04_saas/01_idpへ接続）
- 次の仮説
  - 仮説A：GitHubは公式だが、PagesやDocsが別ホストとして入口を増やしている
    - 次：03_http/12_waf_cdn で外周境界（同一/別）を確認し、02_web/01_web_00_reconへ入口追加
  - 仮説B：公式らしさが弱く、対象外混入が疑わしい
    - 次：17_rdap_whois（組織境界）や公式サイトリンクで強度を上げる。上がらなければ候補止まりで退避
  - 仮説C：CI由来の漏えい経路の匂いがある
    - 次：06_config_02（Secrets）へ“経路”として渡し、値の扱いではなく運用改善観点で整理する

## 次に試すこと（仮説A/Bの分岐と検証）
- 仮説A（公式・入口増加）：
  - 次の一手：
    - Pages/DocsのURLを 02_web/01_web_00_recon の入口として登録し、境界（WAF/CDN）を12_waf_cdnで観測
    - 10_ctlog（SAN）と突合し、証明書運用が同じか別かで境界説明を固める
  - 到達点：
    - “GitHub由来の別入口”を証跡付きで説明し、検証計画に反映できる

- 仮説B（候補止まり）：
  - 次の一手：
    - 公式リンク/ドメイン整合/CT/SANの束で強度を上げられない場合は、候補として保管し深掘りしない
  - 到達点：
    - 対象外混入によるスコープ逸脱を防げる

- 仮説C（CI/Secrets経路の匂い）：
  - 次の一手：
    - `.github/workflows` の “変数名/参照/出力” のみを抽出し、06_config_02へ渡す（値の追跡はしない）
    - 04_saas（監査ログ）で、GitHub側の監査・運用観点へ繋げる
  - 到達点：
    - “漏えい経路”を運用設計として整理できる

## 手を動かす検証（Labs連動：観測点を明確に）
- 関連 labs：
  - `04_labs/01_local/01_attack-box_作業端末設計.md`（証跡の保存・差分管理）
- 取得する証跡（目的ベースで最小限）：
  - `github_candidates.csv`：org/repo/pages_url, evidence(公式リンク等), confidence(強/中/弱), notes
  - `github_extracted_surface.csv`：source(repo/pages), extracted_type(endpoint/env/third-party/secret_path), value, confidence
  - スクリーンショット or URLログ（調査根拠として）

## コマンド/リクエスト例（例示は最小限・意味の説明が主）
~~~~
# 目的：GitHub公開リポジトリから“入口/境界”の断片を抽出する（例示：ローカルでの最小実施）
# 注：対象の同意・スコープに沿って実施する。公開情報の閲覧に限る。

# (1) 公開リポジトリを取得（必要なら）
git clone https://github.com/<org>/<repo>.git

# (2) endpoint候補を粗抽出（/api, /graphql, webhook, admin 等）
grep -RInE "https?://|/api/|/graphql\b|/webhook\b|/admin\b|oauth|saml" <repo_dir> | head -n 50

# (3) CI設定（GitHub Actions）から“変数名/連携”の匂いを抽出（値は追わない）
grep -RInE "secrets\.|AWS_|AZURE_|GCP_|OIDC|SAML|token|client_id" <repo_dir>/.github/workflows | head -n 50
~~~~
- 観測していること：
  - 公開物からの入口（URL）・外部依存（SaaS/クラウド）・Secrets漏えい経路（出力/参照）の可能性

## 参考（必要最小限）
- `01_topics/01_asm-osint/10_ctlog_証明書拡張観測（SAN_ワイルドカード_中間CA）.md`
- `01_topics/01_asm-osint/12_waf-cdn_挙動観測（ブロック_チャレンジ_例外）.md`
- `01_topics/02_web/06_config_02_Secrets管理と漏えい経路（JS_ログ_設定_クラウド）.md`
- `01_topics/04_saas/02_saas_共有・外部連携・監査ログの勘所.md`

## リポジトリ内リンク（最大3つまで）
- 関連 topics：`01_topics/02_web/06_config_02_Secrets管理と漏えい経路（JS_ログ_設定_クラウド）.md`
- 関連 playbooks：`02_playbooks/01_asm_passive-recon_資産境界→優先度付け.md`
- 関連 labs：`04_labs/01_local/01_attack-box_作業端末設計.md`