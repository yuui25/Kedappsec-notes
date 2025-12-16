# 15_robots_sitemap_公開ディスカバリ（入口_禁止領域_優先度）.md

## ガイドライン対応（ASVS / WSTG / PTES / MITRE ATT&CK）
- ASVS：
  - V1（アーキテクチャ/境界）とV14（運用・構成）を支える前提として、公開ディスカバリ（robots/sitemap）が示す「入口」「公開/非公開の意図」「運用上の例外パス」を境界情報として固定する。
- WSTG：
  - Information Gathering（入口/コンテンツの特定）として、公開メタ情報（robots.txt, sitemap.xml）からエンドポイント候補を抽出し、Web Recon（入口→境界→検証方針）へ繋げる。
- PTES：
  - Intelligence Gathering（公開情報からの入口抽出）→ Threat Modeling（例外パス/管理導線の仮説）→ Vulnerability Analysis へ接続する。
- MITRE ATT&CK：
  - Reconnaissance（公開情報からの探索効率化）として位置づける。目的は回避や侵入ではなく、診断の入口優先度を上げるための観測と状態化。

## 目的（この技術で到達する状態）
- robots.txt / sitemap.xml（および sitemap index）から、**入口（URL/パス）** を機械的に抽出し、02_web の入口設計へ渡せる。
- `Disallow` や `Noindex` の“意図”を、秘匿と誤認せずに、**運用境界・例外パスの候補**として扱い、優先度付け（見る/見ない）を決められる。
- 12_waf_cdn（外周挙動）・03_http（挙動/ヘッダ）と合わせ、入口の到達性（200/30x/403/404）を yes/no/unknown で状態化できる。

## 前提（対象・範囲・想定）
- 対象：
  - 代表ホスト（ルートドメイン、主要サブドメイン：login/api/admin等がある場合は別扱い）
  - `https://<host>/robots.txt`
  - `https://<host>/sitemap.xml`（および robots 内の `Sitemap:` 指定）
- 想定：
  - robotsは “アクセス制御” ではない（単にクローラ指示）。ただし「運用者が意識している領域」のシグナルにはなる。
  - sitemapはSEO目的であり、全入口を網羅しない（逆に、公開したくない入口が誤って載ることもある）。
  - CDN/WAF配下で、robots/sitemapだけ別挙動（キャッシュ/403/リダイレクト）になることがある（12_waf_cdnへ接続）。
- できること/やらないこと：
  - できる：公開ファイルの取得、URL抽出、最小限の到達性確認（代表点/少数）
  - やらない：抽出URLを根拠に総当たりアクセス、負荷試験、スコープ外拡大

## 観測ポイント（何を見ているか：プロトコル/データ/境界）
### 1) robots.txt で見るべき情報（“禁止”ではなく“意図”）
- `Disallow:`：
  - 重要なのは「ここに何が並ぶか」。例：`/admin` `/internal` `/api` `/stg` `/debug` 等は機能境界のシグナル。
  - ただし Disallow は“アクセス禁止”ではないため、存在＝秘匿と誤解しない。
- `Allow:`：
  - 例外的に許可したい公開範囲が見える（運用上の境界）。
- `Sitemap:`：
  - sitemapの入口（複数/別ホスト/別パス）を列挙できる。これが本命導線になることが多い。

### 2) sitemap.xml / sitemap index の構造（入口の母集団）
- sitemap（urlset）：
  - `<loc>` のURLを抽出して入口候補にする（パス規則/カテゴリ/機能境界が見えることが多い）。
- sitemap index（sitemapindex）：
  - 複数のsitemapへ分割される。分割単位（blog/product/help/api等）がそのまま境界になることがある。
- 重要な差分：
  - `lastmod`（更新頻度の匂い）＝“現役入口”の優先度付けに使える
  - URLの規則（`/admin/` `/api/` `/v1/` `/oauth/` 等）＝次工程（authn/authz/api）へ直結

### 3) “到達性”と“外周の成立点”を同時に見る
robots/sitemapで得たURLは、次の状態に落とす（yes/no/unknown）。
- yes：200/30xで到達（入口として成立）
- no：404/NXDOMAIN等（過去資産、環境差、誤記の可能性）
- unknown：403/チャレンジ/レート等で観測が歪む（12_waf_cdnで境界を先に確定）

### 4) 境界（資産/信頼/権限）に落とす
- 資産境界：
  - robots/sitemapが「どのホストで提供されるか」（同一ホスト/別ホスト/CDN）を固定する。
  - hostごとに別ファイル・別規則なら、別アプリ/別運用の可能性（11_cohostingと接続）。
- 信頼境界：
  - sitemapが外部ホスト（別ドメイン/第三者）を指す場合は第三者境界のシグナル（04_saas/01_idpにも接続し得る）。
- 権限境界：
  - `/admin` `/console` `/manage` 等が含まれる場合、02_web/03_authz（境界モデル化）の優先度が上がる。
  - ただし “見える＝アクセスできる” ではない。あくまで入口候補として扱う。

## 結果の意味（その出力が示す状態：何が言える/言えない）
- 言えること（状態）：
  - 公開ディスカバリとして、入口候補（URL/パス）を抽出できる
  - “運用者が意識している領域”（Disallow/分割sitemap）を、境界の仮説として立てられる
  - 到達性（yes/no/unknown）を最小観測で揃え、次工程の優先度が決まる
- 言えないこと（断定しない）：
  - Disallowに載る＝秘匿情報、あるいは脆弱、の断定
  - sitemapに載らない＝存在しない、の断定（動的URL/会員向け/内部向けは載らないことが多い）

## 攻撃者視点での利用（意思決定：優先度・攻め筋・次の仮説）
- 優先度が上がる状態：
  - Disallowに管理/内部/デバッグ系が多い（境界が濃い）
  - sitemapが機能単位で分割（境界が明確）され、特定領域が頻繁更新（lastmodが新しい）
  - robots/sitemapがサブドメインや別ドメインを指す（境界が増えている）
- 次の仮説：
  - 仮説A：入口は多いが、外周（WAF/CDN）で到達性観測が歪む
    - 次：12_waf_cdnで成立点を固め、unknownを減らしてからWeb reconへ渡す
  - 仮説B：管理系/認証系入口が見える
    - 次：02_web/02_authn・03_authz・04_api へ直結（入口→境界モデル→検証観点）
  - 仮説C：sitemapがSEO用途中心で、攻撃面に直結しない
    - 次：14_js_sourcemap や 04_js（フロント由来）で endpoint を増やす方へ寄せる

## 次に試すこと（仮説A/Bの分岐と検証）
- 仮説A：403/チャレンジで unknown が多い
  - 次に試すこと：
    - 12_waf_cdn の代表点（/robots.txt, /sitemap.xml）で応答型（ブロック/通常）を状態化
    - 同一条件で少数URLだけ到達性確認し、unknownが外周起因かを切る
  - 到達点：
    - “入口抽出はできたが外周で観測が歪む” を証跡つきで説明できる

- 仮説B：管理/認証/APIに繋がる入口が取れた
  - 次に試すこと：
    - 抽出URLを機能境界（admin/auth/api）で整列し、02_web/01_web_00_recon へ入口として渡す
    - 入口がAPIなら 04_api_00/01 の権限伝播モデルへ繋ぐ
  - 到達点：
    - “入口→境界→検証方針” が自然に決まる

- 仮説C：入口は取れたが、過去資産や誤記が混ざる
  - 次に試すこと：
    - 09_passive-dns / 10_ctlog と突合し、過去入口（last_seen/発行履歴）と現在入口を分離
    - “現役入口”だけをWeb側に渡し、過去入口は別枠で保管（証跡として）
  - 到達点：
    - 入口母集団が整理され、無駄な深掘りが減る

## 手を動かす検証（Labs連動：観測点を明確に）
- 関連 labs：
  - `04_labs/01_local/02_proxy_計測・改変ポイント設計.md`（観測条件の固定）
  - `04_labs/01_local/03_capture_証跡取得（pcap_harl_log）.md`（robots/sitemap取得の証跡）
- 取得する証跡（目的ベースで最小限）：
  - `robots.txt`（raw保存）
  - `sitemap.xml`（raw保存、indexの場合は分割sitemapも“代表1〜2本”だけ保存）
  - 抽出URLの一覧（csv）と、到達性（status）の最小観測

## コマンド/リクエスト例（例示は最小限・意味の説明が主）
~~~~
# 目的：robots/sitemapから入口候補を抽出し、少数だけ到達性（yes/no/unknown）を付ける

# (1) robots取得（証跡）
curl -sk https://example.com/robots.txt -o robots.txt

# (2) robots内のSitemap行を抽出（複数あり得る）
grep -i '^sitemap:' robots.txt

# (3) sitemap取得（代表）
curl -sk https://example.com/sitemap.xml -o sitemap.xml

# (4) <loc> を抽出して入口候補化（正規表現は最小でよい）
grep -oE '<loc>[^<]+' sitemap.xml | sed 's/<loc>//' | sort -u > sitemap_urls.txt

# (5) 代表点だけ到達性確認（多すぎる場合は先頭N件に限定）
head -n 30 sitemap_urls.txt | while read -r u; do
  code=$(curl -sk -o /dev/null -w "%{http_code}" "$u")
  echo "$code,$u"
done > sitemap_urls_status.csv
~~~~
- 観測していること：
  - 公開ディスカバリの入口（robots/sitemap）と、入口の到達性（yes/no/unknown）の最小確定

## 参考（必要最小限）
- `01_topics/01_asm-osint/03_http_観測（ヘッダ・挙動）と意味.md`
- `01_topics/01_asm-osint/12_waf_cdn_挙動観測（ブロック_チャレンジ_例外）.md`
- `01_topics/02_web/01_web_00_recon_入口・境界・攻め筋の確定.md`

## リポジトリ内リンク（最大3つまで）
- 関連 topics：`01_topics/02_web/01_web_00_recon_入口・境界・攻め筋の確定.md`
- 関連 playbooks：`02_playbooks/01_asm_passive-recon_資産境界→優先度付け.md`
- 関連 labs：`04_labs/01_local/03_capture_証跡取得（pcap_harl_log）.md`