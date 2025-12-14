<<<BEGIN>>>
# 02_authn_04_sso_oidc_flow観測（state_nonce_code_PKCE）.md

## 目的（この技術で到達する状態）
- OIDC（主に Authorization Code / PKCE）で「どこからどこまでが認証の責務か」を、通信の観測で説明できる（アプリ／IdP／ブラウザの役割分担）。
- `state` / `nonce` / `code` / `redirect_uri` / `PKCE` が、どの境界（資産・信頼・権限）を守るための要素かを、挙動差分で確定できる。
- “SSOだからブラックボックス”で終わらず、検証の結論を **yes / no / unknown** と根拠（HAR/Proxyログ）で残せる。

## 前提（対象・範囲・想定）
- 対象：Webアプリのログインが外部IdP（OIDC）にリダイレクトされる構成（社内IdP/外部IdPいずれも）。
- 想定フロー：Authorization Code（推奨）＋（必要に応じて）PKCE。
- 観測に必要：ブラウザのHAR、Proxyログ（302のLocation、クエリ、Set-Cookie、/token 呼び出し）を取得できること。
- 注意：検証は **自分が許可された環境・対象** に限定し、値（トークン等）の扱いは最小化（記録はマスク）する。

## 観測ポイント（何を見ているか：プロトコル/データ/境界）
### 1) 入口（アプリ→IdP へのリダイレクト）
- 典型：302/303 の `Location` に `authorize` 相当のURLが入る
- 観測対象（例）：
  - `response_type=code`
  - `client_id`
  - `redirect_uri`
  - `scope`
  - `state`
  - `nonce`（ID Tokenを使う場合に重要）
  - `code_challenge` / `code_challenge_method`（PKCE）
  - `prompt` / `max_age`（再認証やMFAに絡む）
- 境界の意味：
  - **信頼境界**：アプリ→IdP（第三者）へ委譲する瞬間
  - **資産境界**：`code`（交換可能な認証成果物）が“どこを通るか”

### 2) 戻り（IdP→アプリ へのコールバック）
- 典型：アプリ側の `redirect_uri` に `?code=...&state=...` が付いて戻る
- 観測対象：
  - `code` がURLクエリに載る（載ること自体は通常だが、露出・転送・ログ混入が問題になり得る）
  - `state` が戻ってくる（戻らない／固定／欠落は重要な差分）
- 境界の意味：
  - **資産境界**：`code` がブラウザ経由（フロントチャネル）で運ばれる
  - **権限境界**：ここで“誰として扱うか”が確定していく（ただし最終確定は後段が多い）

### 3) コード交換（アプリ→/token）
- 典型：バックエンド（またはBFF）が `code` を `/token` に交換する
- 観測対象：
  - `/token` 呼び出しが **サーバ側** で実行されているか（ブラウザから見えるCORS/API呼び出しになっていないか）
  - `code_verifier` が使われているか（PKCE）
  - 交換後に何がセットされるか（Cookie / セッション / Bearer）
- 境界の意味：
  - **信頼境界**：IdPの“発行物”をアプリが受け取り、内部セッションに変換する境界
  - **資産境界**：トークン・セッションが“どこに保存されるか”が決まる

### 4) セッション確立（アプリ側の最終形）
- 観測対象：
  - Cookie属性（Secure/HttpOnly/SameSite、Path/Domain）
  - API呼び出し時の主体（Authorizationヘッダ or Cookie）
  - ログアウトや再認証時の挙動（失効・更新）
- 境界の意味：
  - OIDCは“外部で本人確認”だが、**アプリ内のセッション管理**が崩れると実害が出る

## 結果の意味（その出力が示す状態：何が言える/言えない）
- `state` が「リクエストごとに変わる」かつ「戻りで一致検証されている」挙動が見える  
  → 期待されるCSRF対策の方向。欠落/固定/不一致でも通るなら **境界が弱い可能性**。
- `nonce` の有無と使われ方が確認できる  
  → ID Token を扱う設計で `nonce` が無い/検証不明なら、**リプレイ・取り違え**の説明が必要になる場合がある（断言せず、unknown を許容）。
- `redirect_uri` が厳格（完全一致）か、緩い（部分一致・ワイルドカード相当）かの差分が取れる  
  → 緩い場合、`code` の受け渡し経路が広がり、**露出面**が増える方向。
- PKCE（`code_challenge`/`code_verifier`）が観測できる  
  → “公開クライアント（SPA等）”でPKCEが見えない場合は、**成立条件の確認**が次の課題になる（即断しない）。
- /token がブラウザから直接叩けているように見える（CORS越し等）  
  → 設計として危険な方向だが、実装意図・例外経路を含めて根拠が必要。

## 攻撃者視点での利用（意思決定：優先度・攻め筋・次の仮説）
- 優先度（まず固める順）
  1) `redirect_uri` の厳格さ（`code` がどこへ戻り得るか＝資産境界の広さ）
  2) `state` の生成と一致検証（“ログインの意図”の境界）
  3) PKCE の有無（コード交換の耐性、特に公開クライアント）
  4) `nonce` とID Tokenの扱い（取り違え・リプレイの余地）
  5) `code` / token の露出（URL、Referer、ログ、JS、監査ログ）
- 状態が意思決定に効く例
  - `state` が固定/欠落でも通る → まずは「一致検証があるか」を差分で確定し、例外経路（別ログイン導線）も同様かを見る。
  - `redirect_uri` が緩い → “戻り先”の資産境界が広い可能性。`code` が通る経路の棚卸し（ログ混入/外部転送）へ。
  - PKCEが見えない → 公開クライアントかどうか（実装形）を確定し、コード交換の主体（ブラウザ/サーバ）を詰める。

## 次に試すこと（仮説A/Bの分岐と検証）
### 仮説A：`state` が毎回同じに見える／無い
- 次に試すこと：同一環境でログインを複数回実施し、`authorize` の `state` とコールバックの `state` を突き合わせる（差分を保存）
- 期待する観測：リクエスト単位で変化するか、欠落時に拒否されるか（yes/no/unknown）

### 仮説B：`redirect_uri` が緩そう（微妙に変えても通る）
- 次に試すこと：`redirect_uri` がどの単位で許容されるか（完全一致/一部一致）を、最小差分で観測する（許可範囲を“事実”として残す）
- 期待する観測：許容範囲が狭い/広いを根拠付きで説明できる

### 仮説C：PKCE が見えない（`code_challenge` が無い）
- 次に試すこと：クライアント種別（SPA/ネイティブ/サーバサイド）と、`/token` 呼び出し主体（ブラウザから見えるか、サーバ側か）を確定する
- 期待する観測：PKCEが必要な前提かどうか、検証の土台（境界）が固まる

### 仮説D：`code` やトークンが露出しているように見える
- 次に試すこと：露出経路を分類して確定（URL、Referer、ログ、JS、外部リソース送信）。証跡はマスクして保存
- 期待する観測：露出が“仕様上の通過点”なのか、“漏えい”なのかを分けて説明できる

## ガイドライン対応（ASVS / WSTG / PTES / MITRE ATT&CK：毎回）
- ASVS：
  - V2（Authentication）：SSOフローの正当性（state/nonce 等）、認証イベントの整合性
  - V3（Session Management）：SSO後のセッション確立（Cookie属性、失効、更新）
  - V4（Access Control）：ログイン後の主体・スコープが操作権限へどう反映されるか（伝播の前提）
  - V9（Communications）：リダイレクトやトークン交換の通信保護（TLS前提、誤露出の抑止）
  - V13（API）：/tokenやバックエンド連携がAPI境界として安全に扱われるか
- WSTG：
  - Authentication / Session：SSO導線の確認、state/nonce、セッション確立と失効の観測
  - Identity Management（該当する場合）：IdP連携における主体の取り扱い（取り違え防止）
  - Client-side（該当する場合）：URL/JS/HARに機微情報が露出しないか（露出経路の分類）
- PTES：
  - Intelligence Gathering → Vulnerability Analysis：フロー観測で境界を確定し、差分で成立条件を切る
  - Reporting：yes/no/unknown を証跡（HAR/Proxyログ）で説明可能にする
- MITRE ATT&CK：
  - Credential Access / Collection：トークンやコード等“代替認証材料”の収集に接続
  - Initial Access / Valid Accounts：外部認証を足場に主体を得る、という目的に接続
  - Defense Evasion：失効・再認証・MFA例外の有無が意思決定に影響

## 手を動かす検証（最小：観測→差分→記録）
~~~~
# 目的：OIDCフローを「入口→戻り→交換→セッション確立」の4点で固める（値はマスク）

# 1) 入口（authorize）を保存
# - 302 Location のクエリ（response_type, client_id, redirect_uri, scope, state, nonce, code_challenge）
# 2) 戻り（callback）を保存
# - redirect_uri に付く code/state（値は伏せて“有無・形式”を記録）
# 3) 交換（/token）が見えるか確認
# - ブラウザから見えるか？サーバ側のみか？（観測可能範囲を明記）
# 4) セッション確立の形を保存
# - Set-Cookie / Authorization 利用形 / 失効（ログアウト）挙動

# 記録テンプレ（ケースに転記できる最小）
# - 入口URL/コールバックURL（パスのみでも可）
# - state/nonce/PKCE の有無（yes/no/unknown）
# - redirect_uri の許容範囲（完全一致/それ以外/unknown）
# - /token の呼び出し主体（browser/server/unknown）
# - 結論（yes/no/unknown）＋根拠（HAR/Proxyログの位置）
~~~~

## 参考（必要最小限）
- 親（入口）：`01_topics/02_web/02_authn_00_認証・セッション・トークン.md`
- 関連（トークン）：`01_topics/02_web/02_authn_03_token設計（Bearer_JWT_Refresh_Rotation）.md`
- 関連（SaaS/IdP境界）：`01_topics/04_saas/01_idp_連携（SAML OIDC OAuth）と信頼境界.md`
- 観測（Labs）：`04_labs/01_local/02_proxy_計測・改変ポイント設計.md`

---
<<<END>>>
