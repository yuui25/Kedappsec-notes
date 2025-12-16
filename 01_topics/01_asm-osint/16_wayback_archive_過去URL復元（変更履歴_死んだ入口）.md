# 16_wayback_archive_過去URL復元（変更履歴_死んだ入口）.md

## ガイドライン対応（ASVS / WSTG / PTES / MITRE ATT&CK）
- ASVS：V1（アーキテクチャ/境界）・V14（運用/構成）を支える前提として、過去URL/過去コンテンツの痕跡から「残存導線」「環境差分」「運用上の例外」を状態化し、検証範囲と責任分界をブレさせない。
- WSTG：Information Gathering の一部として、現在のrecon（06/09/10/15）では見えない過去の入口（URL/パス/機能）を復元し、Web側（入口→境界→検証方針）に渡す。
- PTES：Intelligence Gathering（過去→現在の変遷復元）→ Threat Modeling（残存/例外/移行ミスの仮説）→ Vulnerability Analysis へ接続する。
- MITRE ATT&CK：Reconnaissance（公開情報からの情報収集・変遷把握）として位置づけ、攻撃者が「忘れられた入口」を探す論理を、診断側の優先度付けに転用する。

## 目的（この技術で到達する状態）
- Wayback/Archive（Webアーカイブ）から、**過去に公開されていたURL（パス・パラメータ含む）** を復元し、入口候補を増やせる。
- 復元URLを「現役/過去/不明（unknown）」に分類し、**いま見るべき入口（優先度）** と **証跡として残すべき過去資産** を分離できる。
- 09_passive-dns（DNS履歴）・10_ctlog（発行履歴）・15_robots_sitemap（公開ディスカバリ）と結合し、過去→現在の移行や残存導線を説明できる。

## 前提（対象・範囲・想定）
- 対象：
  - ルートドメイン＋主要サブドメイン（例：`example.com`, `api.example.com`, `admin.example.com`）
  - 06_subdomain/15_robots_sitemap/14_js_sourcemap で得た入口候補のホスト群
- 想定される落とし穴：
  - アーカイブは網羅ではない（観測対象/クロール頻度/robots影響/過去の到達性に依存）
  - アーカイブに残るURLは「現在も有効」とは限らない（むしろ“無効が混ざる”前提）
  - 認証が必要な領域は残りにくいが、**ログイン前導線（/reset /register /oauth/callback 等）** は残ることがある
- できること/やらないこと：
  - できる：公開アーカイブからURLを収集し、最小限の到達性確認で状態化する
  - やらない：収集URLを根拠に大量アクセスや総当たりを行う（負荷・スコープ逸脱回避）

## 観測ポイント（何を見ているか：プロトコル/データ/境界）
### 1) 収集単位は「URL（スキーム/ホスト/パス/クエリ）」で固定する
- DNSやCTは「ホスト」が中心だが、Waybackは「URLの履歴」そのものが対象。
- 入口として意味があるのは、パスとクエリ（パラメータ）：
  - 例：`/api/v1/...`、`/admin/...`、`/debug`、`/swagger`、`/graphql`
  - 例：`?redirect=`、`?returnTo=`、`?next=`、`?callback=` 等（認証/遷移境界に直結）

### 2) “復元したいのはページではなく導線”である（判断を揃える）
- 重要なのは「何が存在したか」より「どんな導線/機能境界があったか」：
  - 認証導線：`/login` `/logout` `/reset` `/mfa` `/oauth/*` `/saml/*`
  - 管理導線：`/admin` `/console` `/manage` `/internal`
  - API導線：`/api` `/graphql` `/v1` `/swagger` `/openapi`
  - 環境導線：`/stg` `/dev` `/test` `/old` `/backup`

### 3) “現状確認”は最小でよい（yes/no/unknown を作る）
復元URLは、次の状態に落とす（入口の優先度付けが目的）。
- yes：現状で到達（200/30x/認証リダイレクト等）＝入口候補として採用
- no：現状で到達しない（404/NXDOMAIN等）＝過去資産として退避（証跡）
- unknown：外周（403/チャレンジ/レート）や到達性条件で観測が歪む＝12_waf_cdnやネットワーク視点で先に境界確定

### 4) “時系列”を使って優先度を付ける
- last_seen（最後にアーカイブされた時期）が新しいほど、残存・再利用・運用漏れの可能性が上がる（断定しない）。
- 逆に古いURLは、以下の価値が残る：
  - パス規則（アプリ構造）や機能境界（当時の設計）を示す
  - リダイレクト/移行先の痕跡（現行の入口に繋がる可能性）

### 5) 境界（資産/信頼/権限）に落とす
- 資産境界：
  - 過去に存在したホスト/パスが、現在どのホストへ移ったか（移行の痕跡）
  - “古い導線が残存している”可能性（入口の二重化）を状態として提示できる
- 信頼境界：
  - 外部連携（IdP/決済/計測）のURL断片が過去ページに残ることがある（第三者境界の棚卸し）
  - 過去のCDN/ホスティング痕跡（別ドメイン・別ホストへの遷移）を境界情報として扱う
- 権限境界：
  - 管理/認証/コールバック導線が見える場合、02_web の authn/authz/api の優先度が上がる
  - パラメータ導線が見える場合、オープンリダイレクト/IDOR/権限伝播の入口候補として扱える（断定はしない）

## 結果の意味（その出力が示す状態：何が言える/言えない）
- 言えること（状態）：
  - 過去に公開されていたURLパターン（機能境界/導線/パラメータ）を復元できる
  - 現状での到達性（yes/no/unknown）を最小観測で付与し、入口の優先度を更新できる
  - 過去→現在の移行痕跡（旧導線/新導線）を説明できる
- 言えないこと（断定しない）：
  - アーカイブに載るURLが必ず対象範囲（第三者・転用・買収等で変わり得る）
  - 過去URLの脆弱性有無（ここは入口設計。検証は02_web側で行う）

## 攻撃者視点での利用（意思決定：優先度・攻め筋・次の仮説）
- 優先度が上がる状態：
  - last_seen が新しい管理/認証/コールバック導線が見つかる
  - パラメータ導線（redirect/returnTo/next 等）が多い（境界系バグの入口候補）
  - `/swagger` `/openapi` `/graphql` 等が見つかる（API観測の入口）
- 次の仮説：
  - 仮説A：過去URLが現状でも一部生きている（残存導線）
    - 次：02_web/01_web_00_recon に渡して、入口→境界→検証方針を更新する
  - 仮説B：過去URLは死んでいるが、設計痕跡が濃い（移行/分割/統合の痕跡）
    - 次：09_passive-dns/10_ctlog と突合して、移行時期・境界変化点を固める
  - 仮説C：403/チャレンジが多く unknown が増える（外周で観測が歪む）
    - 次：12_waf_cdn で成立点を固定し、観測条件を揃えてから再評価する

## 次に試すこと（仮説A/Bの分岐と検証）
- 仮説A（残存導線がある）：
  - 次の一手：
    - 残存URLを機能境界（auth/admin/api）で整列し、02_webへ入口として渡す
    - 入口がAPIなら 02_web/04_api の権限伝播モデルへ接続する
  - 到達点：
    - “過去由来だが現役”の入口として、証跡付きで優先度を上げられる

- 仮説B（死んだが設計痕跡が濃い）：
  - 次の一手：
    - URLパターン（/v1 /legacy /old 等）を抽出し、現行ホスト/パスに似た導線がないかを探索（限定的に）
    - 10_ctlog/09_passive-dns と突合し、過去ホストが別ホストへ移った可能性を整理する
  - 到達点：
    - “移行の痕跡”として境界説明ができ、入口探索の無駄が減る

- 仮説C（unknownが多い）：
  - 次の一手：
    - unknownの原因（403/チャレンジ/レート）を12_waf_cdnで分類し、観測条件を揃える
    - それでもunknownなら「外周境界として unknown を残す」判断を明示する（無理に確定しない）
  - 到達点：
    - 観測限界を境界として扱い、以降の検証の再現性を落とさない

## 手を動かす検証（Labs連動：観測点を明確に）
- 関連 labs：
  - `04_labs/01_local/02_proxy_計測・改変ポイント設計.md`（観測条件の固定）
  - `04_labs/01_local/03_capture_証跡取得（pcap_harl_log）.md`（到達性確認の証跡）
- 取得する証跡（目的ベースで最小限）：
  - `wayback_raw.json`（取得元・取得日時つき）
  - `wayback_urls.csv`（url, first_seen, last_seen, category, status_now）
  - `wayback_notes.md`（yes/no/unknown、代表点、次に回す先）

## コマンド/リクエスト例（例示は最小限・意味の説明が主）
~~~~
# 目的：WaybackからURL一覧を取得し、機能境界で分類→少数だけ現状到達性を付ける

# (1) Wayback CDX APIでURL一覧（例：サブドメイン含む）を取得
# ※大量取得は避ける。まずはドメイン単位で“母集団の形”を見る。
curl -s "https://web.archive.org/cdx/search/cdx?url=*.example.com/*&output=json&fl=timestamp,original&collapse=urlkey" \
  > wayback_raw.json

# (2) URL抽出（正規化して重複除去）
python -c "import json,sys; d=json.load(open('wayback_raw.json')); \
urls=[row[1] for row in d[1:]]; \
print('\n'.join(sorted(set(urls))))" > wayback_urls.txt

# (3) 代表点だけ現状到達性を確認（多すぎる場合は先頭N件に限定）
head -n 50 wayback_urls.txt | while read -r u; do
  code=$(curl -sk -o /dev/null -w "%{http_code}" "$u")
  echo "$code,$u"
done > wayback_status_now.csv
~~~~
- 観測していること：
  - 過去URLの復元（入口候補の作成）と、現状での入口成立（yes/no/unknown）の最小確定

## 参考（必要最小限）
- `01_topics/01_asm-osint/09_passive-dns_履歴と再利用（過去資産の掘り起こし）.md`
- `01_topics/01_asm-osint/10_ctlog_証明書拡張観測（SAN_ワイルドカード_中間CA）.md`
- `01_topics/01_asm-osint/12_waf-cdn_挙動観測（ブロック_チャレンジ_例外）.md`
- `01_topics/02_web/01_web_00_recon_入口・境界・攻め筋の確定.md`

## リポジトリ内リンク（最大3つまで）
- 関連 topics：`01_topics/02_web/01_web_00_recon_入口・境界・攻め筋の確定.md`
- 関連 playbooks：`02_playbooks/01_asm_passive-recon_資産境界→優先度付け.md`
- 関連 labs：`04_labs/01_local/03_capture_証跡取得（pcap_harl_log）.md`