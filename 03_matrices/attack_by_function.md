# attack_by_function — 機能→攻撃の入口マトリクス（初期版）

この表は、**Web機能から“最初に踏むべき入口（attack entry）”を即座に特定**し、詳細は `02_chains/` のカードへ誘導するための索引です。  
`02_chains` は随時更新を前提とし、**代表カード（canonical）**へのリンクは**スラッグ固定**で参照します（ファイル名は変更しない方針）。

> 列の意味：  
> **まず試す入口**＝最優先で当てる攻撃観点 / **入口の見つけ方**＝観測ポイント・兆候 / **前提権限**＝anonymous/user/admin 等 / **典型連鎖(→)**＝到達点までの代表ルート / **主要スタック注意点(タグ)**＝fw/srv/auth/api 等 / **代表カード**＝まず読む1枚（`02_chains/`） / **関連カード**＝増え続ける周辺ケースの検索クエリ

## マトリクス

| Function | まず試す入口 | 入口の見つけ方 | 前提権限 | 典型連鎖(→) | 主要スタック注意点(タグ) | 代表カード | 関連カード |
|---|---|---|---|---|---|---|---|
| auth（ログイン/登録/復旧） | 認証バイパス、アカウント列挙、レート制限回避 | エラーメッセージ差・タイミング差、パスワードリセット/招待リンクの取り扱い、MFAバックアップ経路 | anonymous | auth-bypass→sess→priv | fw:laravel, fw:flask, auth:oidc, srv:nginx | — | 検索: `auth bypass rate-limit password-reset mfa` |
| session（セッション管理） | Cookie属性欠落、トークン再利用、XSS経由のセッショントークン窃取 | `Set-Cookie`属性、トークンのライフサイクル、XSS注入点 | user | sess→priv | srv:nginx, cache, cors | [xss-ref-sess-pii](../02_chains/xss-ref-sess-pii.md) | 検索: `xss session hijack token` |
| account（プロフィール/会員） | IDOR | 連番ID/UUID推測、所有者チェック欠落、Graph差分 | user | idor→priv→admin | fw:flask, api:rest | [idor-basic-priv-admin](../02_chains/idor-basic-priv-admin.md) | 検索: `idor account privilege escalation` |
| file-viewer（ファイル閲覧） | Path Traversal（LFI） | `../`・URL/Unicode二重エンコード、basename/realpath不備 | user | path→lfi→data | srv:apache, srv:nginx | [path-basic-lfi-data](../02_chains/path-basic-lfi-data.md) | 検索: `path traversal lfi file-viewer` |
| file-upload（アップロード） | Content-Type偽装、拡張子/マジック不一致、Polyglot | 拡張子/マジック/CT三点照合の欠落、画像処理系の呼出し | user | upload→file-viewer→path→rce | fw:express, srv:nginx, stor:s3 | — | 検索: `file upload bypass polyglot rce` |
| search（検索） | XSS（反射/DOM）、SQLi/HPP | ハイライト/ソート/フィルタ、`q`/`sort`/`filters[]` | anonymous | xss→sess / sqli→data | cache, cors, hpp | — | 検索: `search xss dom hpp sqli` |
| api-rest（JSON/REST） | IDOR, Mass Assignment, BOLA | `/me` vs `/users/{id}` 差、スキーマの盲信、隠しパラメータ | user | idor→priv / mass→priv | api:rest, auth:oidc | — | 検索: `rest idor bola mass assignment` |
| api-graphql | AuthZ欠落、Field/Depth制御不備、情報過多 | `__schema`露出、深/広クエリ、ownerチェック位置 | user | info→priv / abuse→dos | api:graphql | — | 検索: `graphql introspection authz depth` |
| sso-oauth / oidc | 認証コード漏洩、リダイレクト不備、state不備 | redirect_uriの許可範囲、PKCE/nonce、subの一意性 | anonymous | sso-bypass→sess→priv | auth:oidc | — | 検索: `oauth oidc redirect_uri state pkce` |
| webhook（受信フック） | SSRF、署名検証不備 | 外部到達URLの制御、署名鍵運用、IP許可 | external | ssrf→rce / exfil | ssrf, srv:nginx | — | 検索: `webhook ssrf signature bypass` |
| export/import（CSV/ZIP等） | CSV Injection、ZIP Slip、XXE | Excel関数解釈、パス展開、XMLパーサ設定 | user | import→path→rce / export→exfil | path, xxe | — | 検索: `csv injection zip slip xxe` |
| object-storage（S3等） | ACL/公開設定不備、短命URL運用不備 | プリサインURL有効期限/条件、オリジン&バケット設定 | anonymous | data→exfil / upload→rce | stor:s3, cors | — | 検索: `s3 presigned url acl misconfig` |
| admin（管理画面） | 弱いアクセス制御、CSRF、機能悪用 | 直リンク/ブックマーク、Referer/CSRF対策、操作ログ | user | horiz→priv→admin | authz, csrf | — | 検索: `admin panel idor csrf` |
| messaging（DM/掲示板） | Stored XSS、添付ファイル悪用 | リッチテキスト/プレビュー/通知経路 | user | xss(stored)→sess→priv | xss, file-upload | — | 検索: `stored xss message preview` |
| payment（決済） | 金額改ざん、CSRF、Webhooks認証不備 | クライアント信頼、通貨/小数処理、署名検証 | user | biz-logic→fin | biz-logic, webhook | — | 検索: `payment tamper csrf webhook` |
| notification（通知/メール） | テンプレート注入、リダイレクト悪用 | 変数展開、URL生成、外部リンク誘導 | user | tmpl→xss / open-redirect→phish | ssti, redirect | — | 検索: `template injection redirect` |

## タグ凡例（抜粋）
- **攻撃系**: `idor`, `xss`, `sqli`, `path`, `ssrf`, `xxe`, `hpp`, `cache`, `race`, `biz-logic`, `file-upload`, `rce`, `lfi`  
- **スタック系**: `fw:*`（例: `fw:flask`, `fw:laravel`, `fw:express`）, `srv:*`（`srv:nginx`, `srv:apache`）, `stor:s3`, `auth:oidc`, `api:rest`, `api:graphql`

## 運用ルール
1. **代表カードはスラッグ固定**（例：`idor-basic-priv-admin.md`）。内容は強化しても**改名しない**。  
2. 新規カードを追加したら、該当行の「まず試す入口/見つけ方/典型連鎖」を最小限更新し、**関連カード**の検索語に1語追加。  
3. 行内の**主要スタック注意点(タグ)** は重複を恐れず列挙（検索性優先）。  
4. 月次で、この表に**未リンクの代表カード**がないか軽く目視チェック。

> 補足：`02_chains` 側の見出し（例：`入口`）はテンプレ通り維持してください。アンカーを使う場合は、後続コミットで本表のリンクに `#入口` を付与します。
