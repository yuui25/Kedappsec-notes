# 06_config_03_セキュリティヘッダ（CSP_HSTS_Frame_Referrer）.md

## 目的（この技術で到達する状態）
- セキュリティヘッダを「推奨設定の暗記」ではなく、**ブラウザの挙動を変える“実行境界”** として説明できる（どの攻撃面を狭め、どの攻撃面には効かないか）。
- CSP/HSTS/Frame制御/Referrer-Policy を、**どのエンドポイントで・どの値で・どの主体が** 付与しているか（アプリ/CDN/WAF）を観測し、**yes/no/unknown** で落とせる。
- “ヘッダがある/ない”ではなく、**一貫性（全レスポンス/重要画面/サブドメイン）** と **成立条件（Report-Only/メタタグ/上書き）** を含めて、次の検証（XSS/クリックジャッキング/トークン漏えい/セッション）へ繋げられる。

## 前提（対象・範囲・想定）
- 対象：Webアプリのレスポンスヘッダ（HTML/JSON/静的アセットを含む）、およびそれを付与する経路（アプリ→LB/CDN→WAF→ブラウザ）。
- 範囲：本ユニットは「観測→解釈→意思決定」が主目的。各ヘッダの完全な推奨値カタログ化はしない（必要最小限の“判断に効く差分”に絞る）。
- 依存（前段）
  - `01_topics/01_asm-osint/03_http_観測（ヘッダ・挙動）と意味.md`（観測の基礎）
  - `01_topics/02_web/06_config_00_設定・運用境界（CORS ヘッダ Secrets）.md`（設定・運用境界の親）
  - `01_topics/02_web/06_config_01_CORSと信頼境界（Origin_資格情報_プリフライト）.md`（同じ“レスポンス制御”でもCORSは別物）
  - `01_topics/02_web/05_input_00_入力→実行境界（テンプレ デシリアライズ等）.md`（“実行境界”としての位置づけ）
- 制約：負荷の高い試験や、実ユーザーに影響しうる誘導（クリック誘発等）は前提にしない。観測は **最小差分** で行う。

## 観測ポイント（何を見ているか：プロトコル/データ/境界）
### 1) どこでヘッダが付与されているか（信頼境界：アプリ vs CDN/WAF）
- 同一パスでも、ホスト名（www/api/サブドメイン）やレスポンスタイプ（HTML/JSON/静的）でヘッダの有無や値が変わることがある。
- 重要なのは「そのヘッダが存在するか」ではなく、**“適用される範囲”が攻撃面と一致しているか**（例：ログイン画面だけ欠ける、リダイレクトだけ欠ける、エラー画面だけ欠ける等）。

### 2) CSP（Content-Security-Policy）が“強制”か“観測用”か（成立点）
- `Content-Security-Policy` と `Content-Security-Policy-Report-Only` の区別が核心。
  - Report-Only は **ブロックしない**（防御境界としては未成立）ため、扱いを誤ると誤判定になる。
- メタタグ（`<meta http-equiv=...>`）との二重定義/衝突がある場合、**HTTPヘッダ側が優先されやすい** などブラウザ挙動差が出るため、二重定義の有無を観測する。:contentReference[oaicite:0]{index=0}
- CSPで見る“判断に効く差分”（例）
  - `script-src` の方針（nonce/hash/strict-dynamic の有無、`unsafe-inline`/`unsafe-eval` の有無）
  - `frame-ancestors`（埋め込み制御がCSP側でできているか）
  - `report-uri`/`report-to`（観測のための収集経路があるか＝運用境界）

### 3) HSTS（Strict-Transport-Security）が“全体に効く”か（資産境界：ドメイン）
- HSTSは「以後HTTPSしか使わない」をブラウザに固定するレスポンスヘッダであり、HTTP→HTTPS強制や証明書エラー回避不可などの挙動を持つ。:contentReference[oaicite:1]{index=1}
- 観測で確定すべき点
  - `max-age` の有無と長さ
  - `includeSubDomains` の有無（サブドメインも対象か）
  - “全レスポンス”で返っているか（特定パスやサブドメインで抜けると境界が破れる）:contentReference[oaicite:2]{index=2}

### 4) Frame制御（クリックジャッキング面）をどこで守っているか（権限境界）
- クリックジャッキングは「別サイトのフレームに埋め込まれて、ユーザー操作がすり替わる」問題で、Frame制御は **権限境界（重要操作）** に直結する。:contentReference[oaicite:3]{index=3}
- 観測で見る点
  - `X-Frame-Options`（DENY/SAMEORIGIN 等）
  - CSPの `frame-ancestors`（現代ブラウザではこちらが中核になりやすい）:contentReference[oaicite:4]{index=4}
- “守るべき範囲”の判断
  - ログイン、設定変更、支払い、2FA設定、メール変更など、**ユーザーの意図確認が必要な操作** が対象（全ページ一律DENYが最適とは限らない）。

### 5) Referrer-Policy（外部へのURL漏えい境界）をどう設計しているか
- Referer（Referrer）にはパス/クエリが乗り得るため、OAuth/OIDCの遷移、機微パラメータ、内部パスなどが **第三者へ漏れる境界** になりうる。
- 観測で見る点
  - `Referrer-Policy` の値（何が送られる/送られないか）
  - 外部ドメインへの遷移・外部リソース（分析タグ等）読み込み時に、機微が出ていないか
- ASVSでも Referrer-Policy の適切な設定がコントロールとして扱われる。:contentReference[oaicite:5]{index=5}

### 6) 証跡として残す最小セット（差分が説明できる形）
- 代表URL（最低限）
  - ルート、ログイン、アカウント設定、重要操作（権限境界）、エラー画面、API（JSON）、静的（JS）
- 証跡
  - それぞれのレスポンスヘッダ（raw）
  - “どの経路で付いたか”の推定材料（Server/CDN系ヘッダ差分、ホスト名差分）
  - Report-Onlyの有無（CSPの成立/未成立を決める）

## 結果の意味（その出力が示す状態：何が言える/言えない）
- 言えること（状態として）
  - CSPが強制なら「ブラウザ側の実行境界が狭い」状態（ただしサーバ側脆弱性には効かない）。
  - HSTSが全体に効いているなら「HTTPS強制がブラウザに固定される」状態。:contentReference[oaicite:6]{index=6}
  - Frame制御が適切なら「重要操作がフレーム埋め込み経由で誘導されにくい」状態。:contentReference[oaicite:7]{index=7}
  - Referrer-Policyが適切なら「URL由来の機微が第三者に漏れにくい」状態。:contentReference[oaicite:8]{index=8}
- 言えないこと（境界外）
  - XSS/CSRF/IDOR等の“存在有無”そのもの（ヘッダは多くの場合、影響低減や成立条件の一部）。
  - “安全”の断定（Report-Only、適用範囲の抜け、例外パスがあると意味が変わる）。
- 典型的な結論パターン
  - パターンA：CSP/HSTS/Frame/Referrerが一貫しており、境界が揃っている（攻め筋はサーバ側/認可/入力境界へ寄せる）
  - パターンB：存在するが適用範囲がバラバラ（入口・重要操作・エラー画面など“抜け”を優先して潰す）
  - パターンC：Report-Onlyやメタタグ定義のみ（防御境界は未成立として扱う）:contentReference[oaicite:9]{index=9}

## 攻撃者視点での利用（意思決定：優先度・攻め筋・次の仮説）
- 優先度（最初に確定するもの）
  1) CSPが強制か（Report-Onlyか）／適用範囲が揃っているか
  2) Frame制御が“重要操作”で効いているか（クリックジャッキング面）
  3) Referrer-Policyにより外部へ漏れる情報が抑制されているか（OAuth/OIDC・外部タグ連携が多いほど重要）
  4) HSTSがサブドメイン含めて効いているか（ドメイン境界）
- 状態→攻め筋の分岐（例）
  - CSPが弱い/未成立：ブラウザ実行境界が広い前提で、XSS・DOM経由・外部スクリプト面のリスク評価を上げる（成立条件の検証に進む）。
  - Frame制御が弱い：ユーザー操作誘導（重要操作）を“成立し得る前提”として、重要操作の再認証（step-up）やCSRF防御の評価を優先する。
  - Referrer-Policyが弱い：外部連携（分析タグ/IdP/決済等）におけるURL機微の漏えい（ログ/Referer）を疑い、境界の棚卸しを先に行う。
- ATT&CKへの接続（「目的」で捉える）
  - ブラウザ起点の侵害は Initial Access の Drive-by Compromise（T1189）や、ユーザー行動依存の User Execution（T1204）に接続し得る。:contentReference[oaicite:10]{index=10}
  - その後のセッション奪取・利用は Browser Session Hijacking（T1185）などの目的に繋がる。:contentReference[oaicite:11]{index=11}

## 次に試すこと（仮説A/Bの分岐と検証）
### 仮説A：主要画面・重要操作・サブドメインでヘッダが一貫しており、境界が揃っている
- 次の一手
  - 05_input（入力→実行境界）や 03_authz/04_api（権限伝播）に優先度を移し、ヘッダは“前提OK”として扱う。
  - 例外パス（エラー画面/リダイレクト/静的JS配信）だけを追加観測し、抜けがないことを最小差分で確認する。

### 仮説B：ヘッダは存在するが、適用範囲がバラバラ（入口/重要操作/サブドメインで欠ける）
- 次の一手
  - “抜けている場所”を境界として確定し、そこを起点に攻め筋を組み直す（例：重要操作ページがframe可能、ログインがHSTSなし等）。
  - 付与主体（アプリ/CDN/WAF）を推定して、修正箇所の切り分け（運用設計）に直結させる。

### 仮説C：CSPがReport-Only、またはメタタグ定義のみで実質未成立
- 次の一手
  - CSPは「運用観測段階」と判断し、XSS/DOM起点の影響低減としては未成立扱いにする。:contentReference[oaicite:12]{index=12}
  - 代わりに“どの資産（スクリプト/外部ドメイン）に依存しているか”を観測し、CSP設計の論点（nonce化、外部依存縮小）へ繋げる。

### 手を動かす検証（例示は最小限・意味が主）
~~~~
# 目的：代表エンドポイントの“レスポンスヘッダ差分”を集め、境界（適用範囲）を確定する
# - HTML/JSON/静的JSでそれぞれ1つずつ
# - 値の良し悪しより「有無・一貫性・Report-Only」を最優先で見る

curl -s -D - -o /dev/null "https://app.example.com/" | sed -n 's/^\(Content-Security-Policy.*\|Strict-Transport-Security.*\|X-Frame-Options.*\|Referrer-Policy.*\)/\1/p'
curl -s -D - -o /dev/null "https://app.example.com/login" | sed -n 's/^\(Content-Security-Policy.*\|Strict-Transport-Security.*\|X-Frame-Options.*\|Referrer-Policy.*\)/\1/p'
curl -s -D - -o /dev/null "https://app.example.com/api/me" | sed -n 's/^\(Content-Security-Policy.*\|Strict-Transport-Security.*\|X-Frame-Options.*\|Referrer-Policy.*\)/\1/p'

# 観測の結論は yes/no/unknown でメモする：
# - CSPは強制か？（Report-Onlyではないか）
# - HSTSは includeSubDomains を含むか？
# - 重要操作ページで frame 制御があるか？
# - 外部遷移や外部タグでRefererに機微が出ないか？
~~~~

## ガイドライン対応（ASVS / WSTG / PTES / MITRE ATT&CK：毎回）
~~~~
## ガイドライン対応
- ASVS：V14 Configuration（HTTP Security Headers：CSP/HSTS/X-Frame-Options/Referrer-Policy等）を中心に、必要に応じてV9（通信/TLS）と結合して扱う。ASVSのHTTPヘッダ要件例として、HSTSやReferrer-Policyが明示される。:contentReference[oaicite:13]{index=13}
- WSTG：Configuration and Deployment Management Testing の「Other HTTP Security Header Misconfigurations」を主に対応付け、CSPの二重定義やブラウザ挙動差を“観測ポイント”として扱う。:contentReference[oaicite:14]{index=14}
- PTES：Vulnerability Analysis（境界の欠け/一貫性崩れの同定）→ Exploitation（最小差分で成立条件を確定）→ Reporting（修正箇所：アプリ/CDN/WAFの切り分け）に接続する。:contentReference[oaicite:15]{index=15}
- MITRE ATT&CK：初期侵入/ユーザー行動（T1189 Drive-by Compromise、T1204 User Execution）や、その後のセッション悪用（T1185 Browser Session Hijacking）に繋がる“ブラウザ側境界”として位置づける。:contentReference[oaicite:16]{index=16}
~~~~

## 参考（必要最小限）
- OWASP Cheat Sheet Series：HTTP Security Response Headers Cheat Sheet（ヘッダの役割とX-Frame-Options/CSP関係）:contentReference[oaicite:17]{index=17}
- OWASP WSTG：Test for Other HTTP Security Header Misconfigurations（観測観点）:contentReference[oaicite:18]{index=18}
- OWASP ASVS V14 Configuration：HTTP Security Headers（要件例）:contentReference[oaicite:19]{index=19}
- MDN：Strict-Transport-Security（HSTSのブラウザ挙動）:contentReference[oaicite:20]{index=20}
- 参考（repo内）：`01_topics/02_web/06_config_00_設定・運用境界（CORS ヘッダ Secrets）.md` / `06_config_01_CORSと信頼境界（Origin_資格情報_プリフライト）.md`
