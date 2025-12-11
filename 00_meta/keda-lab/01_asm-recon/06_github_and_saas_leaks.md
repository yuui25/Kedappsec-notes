<<<BEGIN>>>
# STEP1-6 公開ソースコード・SaaS 情報漏えい調査（GitHub / SaaS Exposure Identification）

本ドキュメントは、組織が利用している  
ソースコード管理（GitHub など）や SaaS（Notion / Confluence など）における  
「公開情報からの漏えい」を非侵襲的に調査するための体系と手順をまとめる。

---

## 1. 目的

公開ソースコード・SaaS 情報の調査目的は以下である。

- 公開リポジトリや Wiki による情報漏えいの確認  
- 設定ファイル・トークン・APIキーの誤掲載の検出  
- プロジェクト名や内部環境が推測できる情報の把握  
- CI/CD のビルドログからの公開情報確認  
- SaaS（文書管理）の公開設定の確認  

これらは攻撃者が最初に調べる領域であり、  
ASM でも「外部公開リスク」の中核カテゴリとなる。

---

## 2. 調査対象の体系

### 2.1 公開リポジトリ（GitHub / GitLab）
確認すべき対象：

- ソースコード  
- 設定ファイル（config, env, yml 等）  
- issue / Wiki / Discussions  
- GitHub Pages（静的ホスティング）  

---

### 2.2 公開 CI/CD ログ（GitHub Actions 等）
特徴：

- ビルドログが公開設定の場合がある  
- Secrets が誤ってログに出力される例がある  
- 実行環境の情報（OS / バージョン）も露出する  

---

### 2.3 文書系 SaaS（Notion / Confluence）
特徴：

- 公開スペースが検索エンジンに拾われる場合がある  
- プロジェクト名・内部構成図・要件などが公開されることがある  

---

### 2.4 パッケージ登録・コンテナレジストリ
例：

- npm / PyPI / Maven  
- Docker Hub（public image）  

公開されているパッケージや Docker Image から  
内部構成を推測できるケースがある。

---

## 3. 実践手順（各体系に対応）

### 3.1 GitHub で公開資産を確認（2.1 に対応）

組織名やサービス名で検索する。

~~~~
https://github.com/<org>
https://github.com/search?q=<company>+password
https://github.com/search?q=<company>+api_key
~~~~

確認内容：

- 公開リポジトリの有無  
- 重要キーワード（password / token / secret / api_key）  
- `.env` `.yml` `config.*` の誤公開  
- README / Wiki に機密情報がないか  
- プロジェクト名から内部構造が推測できるか  

---

### 3.2 GitHub Pages / 静的公開サイト（2.1 に対応）

~~~~
https://<org>.github.io/
~~~~

確認内容：

- 公開されている静的ページ  
- フロントコード中の内部 API URL  
- ページ構造からテスト環境の有無を推測  

---

### 3.3 CI/CD ビルドログの確認（2.2 に対応）

GitHub Actions のログ URL（public な場合）を確認する。

~~~~
https://github.com/<org>/<repo>/actions
~~~~

確認内容：

- ビルドログ内に Secrets が誤出力されていないか  
- ビルド環境（OS / Node / Python など）の露出  
- API エンドポイントがログに出ていないか  
- アクセストークンの有無  

---

### 3.4 文書系 SaaS の公開スペース確認（2.3 に対応）

検索エンジンで公開部分を探す。

Notion:
~~~~
https://www.google.com/search?q=site%3Anotion.site+<company>
~~~~

Confluence:
~~~~
https://www.google.com/search?q=site%3Aatlassian.net+<company>
~~~~

確認内容：

- 公開された文書・議事録  
- 内部プロジェクト名  
- システム構成図の公開有無  
- 社内 URL が記載されていないか  

---

### 3.5 パッケージレジストリの確認（2.4 に対応）

npm:
~~~~
https://www.npmjs.com/search?q=<company>
~~~~

Docker Hub:
~~~~
https://hub.docker.com/search?q=<company>
~~~~

確認内容：

- 公開パッケージに内部モジュールが含まれていないか  
- バージョン履歴から内部開発状況を読み取れる場合  
- Dockerfile 内の環境変数の露出  

---

## 4. 注意事項

- 認証を必要とする領域へアクセスしてはならない  
- Private Repository を推測したり強制的に閲覧する行為は禁止  
- 公開設定のページのみを対象とする  
- 外部サービスへの大量リクエストは禁止  
- 情報を入手しても利用・共有には十分注意を払う  

---

## 5. 参考文献

- GitHub Docs: Security Best Practices  
- OWASP Source Code Review Guide  
- MITRE ATT&CK: Reconnaissance  
- NIST SP800-115  
- ISO/IEC 27002:2022 文書管理  

<<<END>>>