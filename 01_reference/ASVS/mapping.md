## V1.1 エンコーディングおよびサニタイゼーションアーキテクチャ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V1.1.1 | L2 | 入力は一度だけ標準形式にデコード／アンエスケープされ、処理前に行うこと（入力バリデーション後に行わない）。 | 部分対応 | 入力のエンコード経路観察、二重デコードの検出（リクエスト差分／侵入テスト） | 実装確認やログ参照があると確実。 | WSTG-INPV-01 | `%253Cscript%253Ealert(1)%253C/script%253E` を送信し二重デコードで `<script>` 実行 | 単一エンコードと二重エンコードの挙動差／反射位置でスクリプト実行を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/01-Testing_for_Reflected_Cross_Site_Scripting |
| V1.1.2 | L2 | 出力エンコーディング／エスケープは最終ステップで行うこと（インタプリタに渡す直前）。 | 部分対応 | 出力のエンコード方法の検証（反射・保存型ペイロードで確認） | ライブラリ自動エスケープの有無はホワイトボックスで評価すると良い。 | WSTG-INPV-02 | 掲示板に `<img src=x onerror=alert(1)>` を保存し表示時に実行 | 保存→閲覧で未エスケープにより XSS が発火する事実を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/02-Testing_for_Stored_Cross_Site_Scripting |

---

## V1.2 インジェクション防御

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V1.2.1 | L1 | HTML/HTTP/CSS/コメント等、各コンテキストに応じた出力エンコードを行うこと。 | 対応可 | XSS向けペイロードテスト、レスポンスコンテキスト解析 | ブラウザ実行コンテキストで確認可能。 | WSTG-INPV-01 | `"><script>alert(1)</script>` を各入力で反射確認 | 反射位置（HTML本文/属性/JS）別に実行可否を観察しエンコード欠如を特定 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/01-Testing_for_Reflected_Cross_Site_Scripting |
| V1.2.2 | L1 | 動的URL構築時はコンテキストに応じたエンコードを行い、危険なプロトコルを許可しない。 | 対応可 | URL生成点のパラメータ操作、プロトコルスキーム検証（javascript:, data: 等拒否） | リダイレクト、open-redirect 諸検査で併せて確認。 | WSTG-CLNT-04 | `next=//evil.tld` や `next=javascript:alert(1)` で遷移/実行誘発 | 外部ドメイン遷移や危険スキーム受理の事実を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/04-Testing_for_Client-side_URL_Redirect |
| V1.2.3 | L1 | 動的に構築する JS/JSON コンテンツは構造を破壊しないようにエンコードすること（JS/JSON 注入防止）。 | 対応可 | JSON/JS を出力するエンドポイントに対するインジェクションテスト | ブラウザ/解析ツールで確認。 | WSTG-CLNT-02 | JSON値に `";alert(1);//` を混入させスクリプト実行を狙う | 生成スクリプトの構文崩壊や実行発火を観察し不適切エンコードを特定 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/02-Testing_for_JavaScript_Execution |
| V1.2.4 | L1 | DB クエリはパラメータ化／ORM 等で保護し、SQL インジェクションを防ぐこと。 | 対応可 | SQLi ペイロード（盲目含む）、エラーベース／時間差検査 | 一部はホワイトボックス/ログ参照で確証。 | WSTG-INPV-05 | `' OR 1=1--`、`'\|\|pg_sleep(5)\|\|'` 等で抽出/遅延を誘発 | レスポンス差/時間差/エラー情報からクエリ介入の成立を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/05-Testing_for_SQL_Injection |
| V1.2.5 | L1 | OS コマンド呼び出しは適切に保護（パラメータ化やエンコード）されていること。 | 部分対応 | コマンドインジェクションペイロードによる検査（入力→外部影響確認） | 実行環境や出力確認が必要なため限定的。 | WSTG-INPV-12 | `;sleep 5;#` や `& ping -c 1 attacker` 等で時間/外部呼出し検証 | 応答遅延や OOB 監視でコマンド実行の事実を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/12-Testing_for_Command_Injection |
| V1.2.6 | L2 | LDAP インジェクション対策が施されていること。 | 部分対応 | LDAP クエリに関する入力を用いた検査（LDAP ベースの機能が存在する場合） | LDAP サービス利用有無に依存。 | WSTG-INPV-06 | `*)(\|(uid=*))` 等でフィルタ合成し認証回避 | 結果件数の異常や認証回避でフィルタ注入成立を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/06-Testing_for_LDAP_Injection |
| V1.2.7 | L2 | XPath/XQuery 等に対するパラメータ化・保護を行うこと。 | 部分対応 | XML/XPath を利用する機能に対するペイロード検査 | サービス構成確認が有効。 | WSTG-INPV-09 | `a' or '1'='1` / `] \| * \| a[1=1]` 等で照合回避 | 認証/検索結果の不正拡大で XPath 注入を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/09-Testing_for_XPath_Injection |
| V1.2.8 | L2 | LaTeX 等外部プロセッサは安全に構成（例: shell-escape 無効）されること。 | 範囲外 | 設定確認を推奨（ドキュメント生成機能が対象の場合は限定検査） | ビルド／環境の確認が必要。 | - | - | - | - |
| V1.2.9 | L2 | 正規表現内の特殊文字を適切にエスケープして誤解釈を防ぐこと。 | 部分対応 | 正規表現処理を伴う入力処理の挙動確認（ReDoS 予兆の検査） | ReDoS は負荷試験注意。 | WSTG-INPV-11 | 入力に `.*$` や `(\w+)+$` などを混入し意図外マッチ/評価遅延を誘発 | 想定外のマッチ成立や顕著な処理遅延を観測し不適切な正規表現処理を特定 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11-Testing_for_Code_Injection |
| V1.2.10 | L3 | CSV／スプレッドシートインジェクション対策（RFC4180 準拠、先頭特殊文字をクオート等）。 | 部分対応 | CSV エクスポート機能に対するフィールド先頭文字検査（エクスポート結果の検査） | エクスポート確認はステージングで慎重に実施。 | WSTG-BUSL-09 | `=HYPERLINK("http://attacker","X")` や `@SUM(1,1)` をセル先頭に含む値を登録→エクスポート | 表計算ソフトで式評価・リンク外出し等が発生する事実を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files |

## V1.3 サニタイゼーション

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V1.3.1 | L1 | WYSIWYG 等からの HTML は既知の安全なサニタイズライブラリで処理すること。 | 対応可 | 保存型 XSS の検証、サニタイズ漏れテスト | DOMPurify 等の利用確認は実装確認で確実に。 | 4.7.2 / WSTG-INPV-02 | WYSIWYG に `<img src=x onerror=alert(1)>` を保存 | 別ユーザで閲覧して JS 実行（保存型 XSS の発火）を確認。反映箇所の出力エスケープ有無を確認。 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/02-Testing_for_Stored_Cross_Site_Scripting |
| V1.3.2 | L1 | eval() や SpEL 等の動的コード実行を使用しない／使用時は入力をサニタイズすること。 | 部分対応 | 動的評価が疑われる箇所のペイロード検査（危険箇所の発見） | 使用有無の把握はホワイトボックスで明確化。 | 4.7.11 / WSTG-INPV-11 | `${T(java.lang.Runtime).getRuntime().exec('id')}` 等の SpEL を入力に混入 | 式評価が行われる入力点へ送信し、サーバ側の実行/例外や機能逸脱の発生を観測。 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11-Testing_for_Code_Injection |
| V1.3.3 | L2 | 危険なコンテキストへ渡すデータは事前サニタイズ・長さ制限等を実施すること。 | 部分対応 | 長さや許可文字検査、入力整合性の確認 | 実装依存。 | 4.7.1 / WSTG-INPV-01 | `q="><script>alert(1)</script>` をパラメータに付与 | 反射表示でスクリプト実行が起こるか（反射型 XSS）を確認。 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/01-Testing_for_Reflected_Cross_Site_Scripting |
| V1.3.4 | L2 | SVG 等スクリプト可能コンテンツはスクリプトや foreignObject を含まないよう検証／サニタイズすること。 | 部分対応 | SVG を受け付ける機能に対する検査（表示と挙動） | ブラウザ差異に注意。 | 4.10.9 | `<svg xmlns="http://www.w3.org/2000/svg" onload="alert(1)"></svg>` をアップロード | 表示時にスクリプトが実行されないこと、危険要素（script/foreignObject）が除去されることを確認。 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files |
| V1.3.5 | L2 | Markdown/CSS/XSL/BBCode 等はサニタイズまたは無効化すること。 | 部分対応 | ユーザ生成コンテンツのレンダリング経路でのインジェクション検査 | 変換パイプライン確認が必要。 | 4.11.3, 4.11.5 | Markdown の `[x](javascript:alert(1))`、スタイル挿入 `color=red;}</style><script>alert(1)</script>` 等 | レンダリング結果で意図しない HTML/CSS が反映・実行されないか確認（許可タグ/属性の制限確認）。 | HTML Injection: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/03-Testing_for_HTML_Injection<br>CSS Injection: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/05-Testing_for_CSS_Injection |
| V1.3.6 | L2 | SSRF 対策としてプロトコル・ドメイン・パス・ポートの許可リスト検証とサニタイズを行うこと。 | 部分対応 | SSRF ペイロード／外部接続検証（リクエスト先制御の検査） | 内部ネットワークへの影響に注意（テスト合意必要）。 | 4.7.19 / WSTG-INPV-19 | `url=http://169.254.169.254/latest/meta-data/`、`gopher://...` 等 | 内部向けリソースへのアクセス可否や応答差分、DNS/リクエストログを確認（SSRF 成立）。 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/19-Testing_for_Server-Side_Request_Forgery |
| V1.3.7 | L2 | テンプレート生成をユーザ入力ベースで動的に許可しないこと。 | 部分対応 | テンプレートエンジン利用箇所のテンプレート注入検査 | 実行環境での確認推奨。 | 4.7.18 / WSTG-INPV-18 | `{{7*7}}` や `{{().__class__.__mro__}}` 等（エンジン依存） | 出力に演算結果が出る/オブジェクト走査が可能か等のレスポンス変化で SSTI を判定。 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/18-Testing_for_Server-side_Template_Injection |
| V1.3.8 | L2 | JNDI クエリ等は信頼できない入力をサニタイズし安全に構成すること。 | 部分対応 | JNDI 利用箇所があればテンプレート注入等を検査 | 利用有無の確認が必須。 | 4.7.6 / WSTG-INPV-06 | `(&(uid=admin)(\|(uid=*)))` 等でフィルタを拡張 | 認証/検索結果が想定外に拡張されるかを観測（LDAP インジェクション）。 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/06-Testing_for_LDAP_Injection |
| V1.3.9 | L2 | memcache 送信前のサニタイズを行いインジェクションを防ぐこと。 | 部分対応 | キャッシュに関連する入力の検査（キャッシュ汚染の観察） | 実運用ログ参照で確証。 | 4.7.17 / WSTG-INPV-17 | `Host: attacker.com` などでキャッシュ汚染（パスワードリセットリンク等の生成元改変） | 複数クライアントから同一 URL 取得で汚染コンテンツが配信されることを確認（Web Cache Poisoning）。 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/17-Testing_for_Host_Header_Injection |
| V1.3.10 | L2 | フォーマット文字列が予期せぬ解決をしないよう事前サニタイズすること。 | 部分対応 | フォーマット処理を伴う機能の入力検査 | 実行時の挙動確認が重要。 | 4.7.13 / WSTG-INPV-13 | `%x%x%x` や `%n` をログ/テンプレータに渡す | 例外・情報漏えい・クラッシュ等の発生を確認（フォーマット文字列インジェクション）。 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/13-Testing_for_Format_String_Injection |
| V1.3.11 | L2 | SMTP/IMAP 等メール注入対策のため、メール送信前のサニタイズを行うこと。 | 部分対応 | メール送信機能の入力検査（ヘッダ注入等） | 実送信は注意。ステージングで検査推奨。 | 4.7.10 / WSTG-INPV-10 | ヘッダに `\r\nBcc: victim@example.com`、IMAP コマンド挿入 | ヘッダ改変/コマンド実行・誤配送やリレー発生の有無を確認。 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/10-Testing_for_IMAP_SMTP_Injection |
| V1.3.12 | L3 | 正規表現による ReDoS を防ぐため、指数的バックトラッキング要素がないことを保証すること。 | 範囲外（負荷試験注意） | ReDoS 予兆検査（負荷試験的手法） | 負荷試験は事前合意必須。 | - | - | - | - |

---

## V1.4 メモリ、文字列、アンマネージドコード

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V1.4.1 | L2 | メモリ安全な文字列操作／安全なメモリコピー等を使用してバッファオーバーフローを防ぐこと。 | 範囲外 | バイナリ／ビルド検査が必要（WebPT は限定的） | バイナリ解析や静的解析が必要。 | - | - | - | - |
| V1.4.2 | L2 | 整数オーバーフロー防止のため符号・範囲・バリデーションを行うこと。 | 範囲外 | 実装／ビルド確認推奨 | ブラックボックスでは判定困難。 | - | - | - | - |
| V1.4.3 | L2 | 動的メモリ解放とダングリングポインタ対策（use-after-free 防止）。 | 範囲外 | 主に開発時／ビルド時検査対象 | WebPT 範囲外（ソース/ビルド確認）。 | - | - | - | - |

---

## V1.5 安全なデシリアライゼーション

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V1.5.1 | L1 | XML パーサを制限構成にして XXE 等を防止する（外部エンティティ無効化等）。 | 対応可 | XXE 攻撃ペイロードによる検査（XML エンドポイント） | パーサ設定確認はホワイトボックスで確実。 | 4.7.7 / WSTG-INPV-07 | `<!DOCTYPE x [<!ENTITY xxe SYSTEM "file:///etc/passwd">]><a>&xxe;</a>` | エンドポイントに送信し外部エンティティ解決や内部ファイル露出・外部通信の有無を確認。 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/07-Testing_for_XML_Injection |
| V1.5.2 | L2 | デシリアライズ時は型ホワイトリスト等で許可型を限定し、安全でないメカニズムは信頼できない入力で使用しない。 | 部分対応 | デシリアライズ対象の入力を操作して異常挙動検査（挙動検証） | 実行時のオブジェクト生成経路の把握が有用。 | 4.7.11 / WSTG-INPV-11 | Java のシリアライズオブジェクト（例: ysoserial/CommonsCollections1）を送付 | サーバ側の例外/挙動変化や任意コード実行の兆候を確認（安全な検証環境で）。 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11-Testing_for_Code_Injection |
| V1.5.3 | L3 | 各種パーサ（JSON/XML/URL）が同一の解析・エンコードを行うよう一貫性を保つこと（相互運用性の問題回避）。 | 部分対応 | 異なるパーサを用いる箇所があれば差分解析の検査 | 実装・設計確認が中心。 | 4.7.4 / WSTG-INPV-04 | `?role=user&role=admin`／`a=1; a=2` 等の重複/汚染でバックエンドとフロントの解釈差を誘発 | 最初/最後勝ちや配列化などのパース差で権限や処理結果が変わるかを確認（HPP）。 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/04-Testing_for_HTTP_Parameter_Pollution |


