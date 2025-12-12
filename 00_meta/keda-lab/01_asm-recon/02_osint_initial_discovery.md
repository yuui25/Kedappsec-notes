# STEP1-2 OSINT 初期発見（Initial Discovery）

本ドキュメントは ASM（Attack Surface Management）における  
外部露出資産の「OSINT 初期調査」を、目的・情報源・実践手順の順で整理する。

---

## 1. 目的

OSINT 初期調査の目的は、公開情報のみを用いて以下を行うことである。

- 組織のインターネット露出資産を把握する  
- 使用している技術スタックの傾向を把握する  
- 潜在的な侵入口候補を洗い出す  

これはペネトレーションテストにおける Recon フェーズ、  
および ASM の自動検出フェーズと一致する。

---

## 2. 情報源の体系

### 2.1 DNS 情報
- A / AAAA：サーバの指し先  
- MX：メール基盤（O365 / Google Workspace など）の把握  
- TXT：SPF / DKIM / DMARC 等の設定  
- CNAME：CDN / クラウドサービスの利用状況推定  

### 2.2 CT（Certificate Transparency）ログ
- 過去に発行された証明書の一覧  
- サブドメインやサービス名の履歴が残る  

### 2.3 ASN / IP レンジ
- 組織が所有する IP ブロック  
- 同一組織が運用する複数ホスト間の関係性  

### 2.4 インターネットスキャン（Shodan / Censys）
- 露出ポート  
- ミドルウェア / WAF の種類  
- VPN / RDP / メールサーバの製品情報  
- TLS 証明書情報  

### 2.5 クラウド露出資産
- AWS S3 / Azure Blob / GCP Storage の公開状態  
- CloudFront / Cloudflare など CDN の存在と挙動  

### 2.6 SaaS / CI/CD 露出
- GitHub リポジトリ・issues・wiki・pages  
- CI/CD のビルドログ  
- Notion / Confluence 等の公開スペース  
- ソースコード・設定ファイル・シークレットの誤公開

---

## 3. 実践手順（各情報源に対応）

以下はいずれも「公開情報の参照のみ」を前提とした手順である。

### 3.1 DNS 情報の確認（2.1 に対応）

~~~~
dig example.com ANY
dig MX example.com
dig TXT example.com
dig CNAME www.example.com
~~~~

確認する内容の例：

- MX からメール基盤（O365 / Google Workspace 等）  
- CNAME から CDN / クラウド（AWS / Azure 等）  
- TXT から SPF / DKIM / DMARC 設定の有無  

---

### 3.2 CT ログの確認（2.2 に対応）

ブラウザで以下のような URL にアクセスする。  
~~~~
https://crt.sh/?q=example.com
~~~~

確認する内容の例：

- 新旧サブドメイン（vpn / dev / stage など）  
- 過去に使われていたサービス名  
- ワイルドカード証明書の有無  

---

### 3.3 ASN / IP レンジ調査（2.3 に対応）

~~~~
whois example.com
whois ASXXXX
~~~~

確認する内容の例：

- 所有している IP ブロックの範囲  
- 自社ラックかクラウドかの区別  
- 廃止されていない古いアドレス帯の有無  

---

### 3.4 Shodan / Censys の参照（2.4 に対応）

~~~~
shodan search "ssl.cert.subject.cn:example.com"
shodan search "org:ExampleCorp"
~~~~

確認する内容の例：

- 露出しているポートとサービス  
- バナーから推測されるソフトウェアとバージョン  
- VPN / RDP / メールサーバ製品（例：Exchange / Fortinet 等）  
- TLS 証明書の有効期限や CN 情報  

---

### 3.5 クラウド露出資産の確認（2.5 に対応）

~~~~
https://<bucket-name>.s3.amazonaws.com/
https://storage.googleapis.com/<bucket-name>/
~~~~

確認する内容の例：

- バケットが存在するか（200 / 403 / 404 などの挙動）  
- 公開状態でないか（一覧取得が可能かどうか）  
- バケット名から推測できるプロジェクト・環境名  

※ アクセス権を要する操作や設定変更は行わない。

---

### 3.6 SaaS / CI/CD 露出の確認（2.6 に対応）

GitHub で組織名やキーワードを検索する。  
~~~~
https://github.com/example
https://github.com/search?q=org%3Aexample+api_key
~~~~

確認する内容の例：

- 公開リポジトリ・issues・wiki・pages の有無  
- API キー / トークン / 接続情報などのハードコード  
- `config.yml` や `.env` 等の設定ファイルの誤公開  

公開ドキュメント系 SaaS について、検索エンジン経由で確認する。  
~~~~
https://www.google.com/search?q=site%3Anotion.site+example
https://www.google.com/search?q=site%3Aatlassian.net+example
~~~~

確認する内容の例：

- Notion / Confluence などの公開スペースの有無  
- URL から推測できるプロジェクト名・内部用語  

CI/CD 公開ログについては、  
GitHub Actions 等でログが「public」になっていないかを確認し、  
ビルドログ内にシークレットや機密情報が出力されていないかをチェックする。

---

## 4. 注意事項

- ポートスキャン、認証試行、ブルートフォース等の能動的攻撃は行わない  
- VDP のスコープ外に対する調査は行わない  
- 取得した情報は社内ルールに従い適切に保管・共有する  
- 調査はあくまで OSINT（公開情報）の範囲に留める  

---

## 5. 参考文献

- MITRE ATT&CK: Reconnaissance  
- OWASP ASVS v4.0.3 V1 Architectural Requirements  
- NIST SP 800-115 Technical Guide to Information Security Testing  
- NIST SP 800-53 Rev.5 RA-5 Vulnerability Monitoring and Scanning  
- ISO/IEC 27002:2022 5.36 公開サービスの管理  
