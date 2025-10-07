05_prompts/maintain/update-attack_by_function-from-card.md

# タイトル
更新プロンプト — attack_by_function を新規カードから自動更新する

# 簡易使い方説明
1. 新規カード本文（`02_chains/<slug>.md` の中身）をコピーする。  
2. （任意）現在の `03_matrices/attack_by_function.md` をコピーしておく（省略可）。  
3. 下のプロンプト（`'''`で囲まれた内容）を ChatGPT に貼り付け、カード本文（A）と（省略しない場合は）マトリクス本文（B）を入力して実行する。  
4. 出力された「置換用1行」を `03_matrices/attack_by_function.md` の該当行にコピペしてコミットするだけ。

'''

プロンプト
以下をそのままコピペして ChatGPT に入力してください。

--- START PROMPT ---
あなたは `Kedappsec-notes` リポジトリのメンテナンスアシスタントです。目的は、新規に作成された `02_chains/<slug>.md` のカード本文を受け取り、その内容に基づいて `03_matrices/attack_by_function.md` の該当する Function 行を自動で更新するための「置換用1行」を生成することです。運用者は出力の「置換用1行」をそのまま GitHub 上の該当ファイルの行にコピペしてコミットします。

--- 入力（運用者が提供） ---
A) 新規カード本文（`02_chains/<slug>.md` の全文） — 必須
B) （任意）現在の `03_matrices/attack_by_function.md` の全文 — 推奨（省略時は web から参照）
C) リポジトリURL（既定）: https://github.com/yuui25/Kedappsec-notes/tree/main/02_chains

--- 出力（あなたが返すもの） ---
1. 置換用1行（必須）: Markdown表の1レコード（`|`区切り、改行なし）。これが運用者が GitHub にコピペする最終文字列です。
2. 差分（任意）: 元行→新行の unified diff（`-`/`+`）を短く提示。
3. 検索語の更新案: 「関連カード」セルへ追加する**1語**（または置換案）。
4. コミットメッセージ案（短文）
5. 注意点（ルール違反や情報不足があれば簡潔に指摘）

--- 生成ルール（厳守） ---
- スラッグ固定: `family-variant[-engine][-pivot][-impact].md` は改名しない。代表カードの差し替えは本文更新で対応。  
- Function 行は原則増やさない（新行が妥当かは下記判定）。  
- 「まず試す入口」は **最大5語**（超過時は要約）。  
- 「主要スタック注意点(タグ)」は **最大3（多くて4）**。`カテゴリ:値` 形式で、カノニカル集合から選択。**攻撃名はここに書かない**。  
  - カノニカル集合（許容タグ例）:  
    - fw: flask, django, express, nest, spring, rails, laravel  
    - srv: nginx, apache  
    - api: rest, graphql, websocket  
    - auth: oidc, saml, oauth2  
    - stor: s3, gcs  
    - lib: imagemagick, libxml, sharp  
    - 設定系: cors, cache, csp
- 「関連カード」セルは既存の検索語に**1語だけ追加**（または1語置換）。
- 代表カードリンクは相対パスで `#入口` 見出しを仮定して出力（例: `[upload-basic-rce](../02_chains/upload-basic-rce.md#入口)`）。
- 出力の列数と `|` の数を厳密に守る（行は改行しない）。

--- Function 判定ロジック（優先順） ---
(1) slug の family を基にマップ（upload→file-upload, path/lfi→file-viewer, idor/authz→account など）  
(2) カード本文の「入口/横展開/到達点」を解析して最も近い既存 Function を選定。  
(3) 曖昧な場合は「最も近い既存行」を優先。新行が真に必要なら運用者に3条件を提示（独立機能、見つけ方が既存と異質、今後カードが複数化見込み）。

--- 生成手順（内部処理フロー） ---
1. 受領したカード本文（A）から `slug` を抽出。family/variant/pivot などを把握する。  
2. Function を決定（上記判定ロジック）。B が与えられれば該当行を抽出。B が無ければ web.run で `03_matrices/attack_by_function.md` と `02_chains` を参照して該当行を特定。  
3. 各セルを生成:  
   - まず試す入口: カードの「入口/横展開」節から名詞句を抽出し、最大5語に要約（「,」で区切る）。  
   - 入口の見つけ方: `ログ/HTTP/モデル/挙動` の観点で最大3短句に要約。  
   - 前提権限: `anonymous / user / admin / external` のどれかに分類。  
   - 典型連鎖: カードの到達点を `→` で短縮（例: `upload→file-viewer→path→rce`）。  
   - 主要スタック注意点(タグ): カノニカル集合から最大3つ選択。  
   - 代表カード: 既存代表があれば据え置き、無ければ今回の slug を代表にする。リンクは相対パスで出力。  
   - 関連カード: 既存の検索語に1語追加（推奨）または置換。

--- 出力例 ---
置換用1行:
| file-upload（アップロード） | CT偽装, 拡張子/マジック不一致, Polyglot, SVGスクリプト, Exif注入 | 三点照合欠落, 画像処理呼出し, サムネ生成 | user | upload→file-viewer→path→rce | fw:express, srv:nginx, stor:s3 | [upload-basic-rce](../02_chains/upload-basic-rce.md#入口) | 検索: `file upload bypass polyglot rce ssti exif` |

差分（任意）:
- | file-upload（アップロード） | Content-Type偽装、拡張子/マジック不一致、Polyglot | 拡張子/マジック/CT三点照合の欠落、画像処理系の呼出し | user | upload→file-viewer→path→rce | fw:express, srv:nginx, stor:s3 | — | 検索: `file upload bypass polyglot rce ssti` |
+ | file-upload（アップロード） | CT偽装, 拡張子/マジック不一致, Polyglot, SVGスクリプト, Exif注入 | 三点照合欠落, 画像処理呼出し, サムネ生成 | user | upload→file-viewer→path→rce | fw:express, srv:nginx, stor:s3 | [upload-basic-rce](../02_chains/upload-basic-rce.md#入口) | 検索: `file upload bypass polyglot rce ssti exif` |

コミットメッセージ案:
feat(matrices): file-upload の入口を強化（SVG/Exif/サムネ生成を追記）

注意点:
- 生成された行は**改行せずそのまま貼る**こと。`-`/`+` は消す。  
- 「まず試す入口」は最大5語。超過時は要約しているか確認すること。

--- END PROMPT ---
