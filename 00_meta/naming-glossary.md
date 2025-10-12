00_meta/naming-glossary.md

# 命名規則・用語集（`<family>-<variant>[-<engine>][-<pivot>][-<impact>]`）

本ドキュメントは `02_chains` のカード名で用いる **語彙の定義と一覧** を提供する。  
目的は「**誰が見ても一貫した粒度で命名でき、検索性・横断性が高い**」状態にすること。  
各トークンは **小文字・半角英数・ハイフン区切り**、3〜12 文字程度を目安とする（省略は可読性最優先）。

---

## 0. 形式と原則

- **形式**：`<family>-<variant>[-<engine>][-<pivot>][-<impact>].md`（角括弧は任意。3〜5 トークン推奨）
- **意味の優先**：`family`（攻撃クラス） ＞ `variant`（入口/手口の型） ＞ `engine`（スタック差） ＞ `pivot`（横展開の方向） ＞ `impact`（到達点/成果）
- **分割基準**：**1 目的 = 1 カード**。環境差で PoC が大幅に変わる場合のみ `engine` を追加し別カード化。
- **更新運用**：新しい略語を使う前に **本ファイルへ追記**。迷う場合は「候補」に置いて実運用で検証。

---

## 1. `<family>`（攻撃クラスの大項目）

**定義**：脆弱性クラス／攻撃技法の上位カテゴリ。  
**使い所**：読者が「どの系統の話か」を 1 語で把握できるようにする。

### 1.1 確定（使用中/推奨）
| token | 意味/範囲 | 代表例・備考 |
|---|---|---|
| xss | Cross-Site Scripting | 反射/格納/DOM などは `variant` で表現 |
| sqli | SQL Injection | UNION/BOOL/時間/エラー 等は `variant` |
| ssrf | Server-Side Request Forgery | IMDS などは `variant=meta` |
| idor | Insecure Direct Object Reference | 多テナント越境は `pivot=tenant` |
| path | Path Traversal / File Path 操作 | LFI/RFI/Zip Slip 等 |
| rce | Remote Code Execution | 入口は `tpl`/`deser`/`cmdi` 等 |
| cmdi | OS Command Injection | `variant` で blind/time 等 |
| deser | Insecure Deserialization | 言語差は `engine` |
| xxe | XML External Entity | inband/oob は `variant` |
| csrf | Cross-Site Request Forgery | SameSite/トークン等の検証 |
| open_redirect | Open Redirect | OAuth 連鎖は `pivot=oauth` |
| tpl | Template Injection（SSTI/CSTI） | ssti/csti は `variant` |
| auth | 認証実装不備 | 弱パス/2FA回避/リカバリ悪用 等 |
| authz | 認可/アクセス制御不備 | 水平/垂直/ポリシ迂回 等 |
| proto_pollution | Prototype Pollution | RCE/SSRF 連鎖は `pivot` |
| http_smuggle | HTTP Request Smuggling | cache 連鎖は `pivot=cache` |
| cache | Cache Poisoning/Deception |  |
| cors | CORS 誤設定 | 認証付き跨り取得可否 等 |
| file_upload | 不適切なファイルアップロード | 拡張子/Content-Type/画像偽装 等 |
| header_inject | ヘッダインジェクション | Host/CRLF/Cache 連鎖 等 |
| subtake | サブドメインテイクオーバー | DNS/ホスティング連携依存 |
| bfl | Business Logic Flaw | 広義。カード粒度に注意 |
| samesite | Cookie SameSite 誤設定群 | `csrf` と交差。表現重複に注意 |

---

## 2. `<variant>`（入口/手口の型）

**定義**：同一 `family` 内での **起点差** を区別する語。検出様式やトリガの違いを表す。  
**表記**：`token | 意味/範囲 | 代表例・備考`（1 行 1 トークン）

### 2.1 確定（使用中/推奨）
| token | 意味/範囲 | 代表例・備考 |
|---|---|---|
| basic | 特段の派生がない最小構成 | まずは最短 PoC で成立する型 |
| ref | 反射型（即時反映） | XSS のリフレクテッド |
| stor | 格納型（サーバ保存） | XSS のストアド |
| dom | DOM ベース | クライアント側処理起点 |
| union | UNION ベース注入 | sqli の結果連結 |
| bool | 真偽（論理）ベース | 盲 SQLi の真偽差 |
| time | 時間差ベース | 盲系（sqli/cmdi 等） |
| error | エラー誘発ベース | sqli のエラー出力活用 |
| stacked | 複数文実行 | DB が複文許可時 |
| oob | 帯域外観測 | DNS/HTTP 経由の外部観測 |
| meta | メタデータ到達 | SSRF→IMDS 等 |
| blind | 応答なし観測 | タイム/外部副作用で判断 |
| nullbyte | ヌルバイト注入 | `%00` による終端/型変換 |
| filter_bypass | フィルタ/ブラックリスト回避 | 2重/Unicode/正規化ずらし |
| gadget | 既知ガジェット連鎖 | 逆直列化のチェーン利用 |
| inband | 同帯域内取得 | XXE 等でアプリ応答に混載 |
| weak_pass | 弱パス許容 | リスト攻撃/総当り耐性不足 |
| 2fa_bypass | 2要素認証回避 | リカバリ/CSRF/ロジック抜け |
| reset_abuse | パスワードリセット悪用 | トークン露出/期限/回数制御不備 |
| h_priv | 水平権限逸脱 | 他ユーザ資産への越権 |
| v_priv | 垂直権限昇格 | 一般→管理 等 |
| policy_bypass | ポリシ/ACL 迂回 | デフォルト許可/オーダ不整合 |
| ssti | サーバ側テンプレ注入 | Jinja/Twig 等（RCE 連鎖） |
| csti | クライアント側テンプレ注入 | Vue/Angular 等 |
| mxss | Mutation XSS | HTML パーサ変換起点 |
| dns | DNS 経由観測/流出 | SQLi/XXE の OOB |
| gopher | gopher プロトコル悪用 | SSRF で任意プロトコル送信 |
| redirect_chain | 多段リダイレクト悪用 | SSRF/CORS/Redirect 絡み |
| symlink | シンボリックリンク悪用 | パス/アップロードで外逸 |
| zip-slip | アーカイブ展開経路逸脱 | `../../` 展開 |
| cl_te | CL→TE 矛盾型 Smuggle | 解析器差を利用 |
| te_cl | TE→CL 矛盾型 Smuggle | 解析器差を利用 |
| double_encode | 二重/重複エンコード | `%252e` 等の正規化回避 |
| unicode | Unicode/正規化差 | NFC/NFD 差異や homoglyph |
| origin_reflection | 要求 Origin 反射 | CORS 誤設定の一形態 |
| wildcard | `*` 許容誤設定 | CORS で Credentials と併用 |
| cred_allowed | 資格情報付き許容 | CORS `Allow-Credentials:true` |
| proto_downgrade | プロトコル降格 | HTTPS→HTTP 誘導 |
| lenient_parse | 寛容パース悪用 | RFC 非準拠許容差利用 |

---

## 3. `<engine>`（スタック差分：PoC/挙動が変わる環境）

**定義**：**環境差で PoC が大幅に変わる** 場合に限定して付与。DB/テンプレート/クラウド/言語基盤/プロキシなど。  
**表記**：`token | 意味/範囲 | 例/備考`（各 token を 1 行で説明）

### 3.1 確定（使用中/推奨）
| token | 意味/範囲 | 例/備考 |
|---|---|---|
| pg | PostgreSQL | sqli 文法・関数差、`copy` 等 |
| my | MySQL/MariaDB | バージョン差/`sleep`/`load_file` |
| ms | Microsoft SQL Server | `xp_cmdshell`/`waitfor delay` |
| ora | Oracle Database | `utl_http`/権限モデル差 |
| sqlite | SQLite | ファイル型/関数制限差 |
| mongo | MongoDB | NoSQL クエリ/演算子挙動 |
| es | Elasticsearch | クエリ DSL/HTTP API 差 |
| redis | Redis | 未認証/CONFIG/モジュール |
| freemarker | Java テンプレート | SSTI 式/サンドボックス差 |
| velocity | Java テンプレート |  |
| thymeleaf | Java テンプレート |  |
| jinja | Python テンプレート |  |
| twig | PHP テンプレート |  |
| mustache | 汎用テンプレート | ロジックレス特性 |
| hbs | Handlebars |  |
| java | Java ランタイム/フレームワーク | 直列化/JNDI/EL 差 |
| dotnet | .NET/ASP.NET | ViewState/直列化 差 |
| php | PHP | `phar://`/ラッパ/魔法関数 |
| python | Python | `pickle`/eval 系 |
| node | Node.js | 原型汚染/モジュール解決 |
| ruby | Ruby | ERB/直列化 差 |
| aws | AWS | IMDS/STS/S3/署名方式差 |
| gcp | Google Cloud | メタデータ/SA 権限 |
| azure | Microsoft Azure | MSI/Managed Identity |
| docker | Docker/コンテナ | ソケット/名前空間 |
| k8s | Kubernetes | サービスアカウント/K8s API |
| nginx | Nginx | 逆代理/ヘッダ規則/CL-TE 差 |
| apache | Apache HTTP Server | mod_proxy/mod_php 差 |
| haproxy | HAProxy | フロント/バック差で Smuggle |
| envoy | Envoy/Service Mesh | 正規化/HTTP/2 差 |
| varnish | Varnish Cache | キャッシュ毒性/パージ |
| spring | Spring 系 | SpEL/デシリアライズ |
| laravel | Laravel | シリアライズ/テンプレ |
| express | Express.js | ミドルウェア順序 |
| django | Django | テンプレ/CSRF/URL ルータ |
| keycloak | Keycloak | OIDC/SAML/トークン処理 |
| okta | Okta |  |
| auth0 | Auth0 |  |
| imds_v1 | AWS IMDSv1 | ヘッダ不要/SSRF 易 |
| imds_v2 | AWS IMDSv2 | セッショントークン必須 |
| adfs | Active Directory Federation Services | SAML/WS-Fed |
| istio | Istio | サイドカー/正規化差 |
| cloudflare | CDN/Proxy | キャッシュ/正規化 |
| fastly | CDN/Proxy |  |
| traefik | Traefik |  |
| pulsar | Apache Pulsar | メッセージ基盤 |
| kafka | Apache Kafka |  |
| rabbitmq | RabbitMQ |  |

> **注意**：`engine` は乱用しない。**同じ PoC で概ね再現できる**なら省略。

---

## 4. `<pivot>`（横展開・連鎖の方向）

**定義**：入口確立後に **どこへ広げるか** を 1 語で示す。読者が「次の一手」を想像しやすくする。

### 4.1 確定（使用中/推奨）
| token | 意味/範囲 | 代表例・備考 |
|---|---|---|
| sess | セッション奪取/固定 | XSS→Cookie/トークン奪取 |
| priv | 権限昇格/役割奪取 | IDOR→管理権限 |
| tenant | 多テナント越境 | ID 指定/ルーティング破綻 |
| intapi | 内部 API 到達 | SSRF→社内 API |
| lfi | ローカルファイル閲覧 | Path→`/etc/passwd` 等 |
| rce | コード実行到達 | Tpl/Deser/Path→RCE |
| portscan | 内部ポート走査 | SSRF でのスキャン |
| oauth | OAuth/OIDC 連鎖 | Redirect→トークン操作 |
| cache | キャッシュ汚染/奪取 | Smuggle/Cache Poisoning |
| dns | DNS 経由観測/流出 | XXE/SQLi の OOB |
| s3 | S3 等のオブジェクト格納到達 | 認証情報流出/横展開 |
| gcs | GCS 到達 | 同上 |
| k8s | K8s API 連鎖 | SSRF→K8s サービスアカウント |
| ldap | 目録サービス連鎖 | 認証/権限情報の横展開 |
| smb | SMB/NTLM 連鎖 | リレー/共有参照 |

---

## 5. `<impact>`（到達点・成果）

**定義**：攻撃連鎖の **実害/ゴール** を 1 語で示す。報告タイトルにも流用しやすい語を採用。

### 5.1 確定（使用中/推奨）
| token | 意味/範囲 | 例 |
|---|---|---|
| admin | 管理者権限取得 | 管理画面/ロール奪取 |
| pii | 個人情報取得 | 氏名/住所/連絡先 等 |
| data | 機密データ取得 | 設定/設計/秘密情報 |
| exec | コード実行 | RCE/任意コマンド |
| ato | アカウント乗っ取り | SSO/リカバリ悪用 |
| dos | 可用性阻害 | 予約枯渇/高負荷 |
| integrity | 改ざん | 支払先/権限変更 |
| fraud | 金銭/不正取引 | 支払/ポイント奪取 |
| ip | 知財流出 | ソース/モデル |
| supply | サプライチェーン汚染 | アップデート改ざん |
| brand | 風評/ブランド毀損 | フィッシング拡散 |
| reg | 規制違反/罰則 | GDPR 等 |

---

## 6. 命名ガイド（実務の流れ）

1. **family を決める**：最も自然な攻撃クラス（例：`xss`）。  
2. **variant を選ぶ**：入口の型（例：`ref`）。  
3. **engine が必要か判定**：PoC が**環境差**で大きく変わるなら付与（例：`-pg`）。  
4. **pivot を付す**：横展開の方向が明確なら 1 語（例：`-sess`）。  
5. **impact で締める**：到達点（例：`-pii`）。

> 例：`xss-ref-sess-pii.md`、`idor-basic-priv-admin.md`、`ssrf-meta-intapi-data.md`、`sqli-union-pg-data.md`

---

## 7. 衝突回避・表記ゆれ

- 同義語は **統一トークン** を採用（例：`open-redirect` に統一、`redirect` は非推奨別名）。  
- 迷ったら「候補」に置いてカード本文で **補足** を入れる。  
- 検索性優先：略語は一般的な**セキュリティ文脈で通じる**形に限定（独自略語は避ける）。

---

## 8. 今後の追加・改訂手順（簡易）

1. 追加したいトークンを **「候補」表** に追記（定義/例/衝突可能性も併記）。  
2. 2〜3 枚の試験運用カードで **可読性/検索性/再現性** を検証。  
3. 問題なければ **「確定」へ昇格**。既存カードを**一括置換**（検索置換指針を `00_meta` に保持）。
