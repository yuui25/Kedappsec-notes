# STEP1-2 OSINT 初期発見（Initial Discovery）

本ドキュメントは ASM（Attack Surface Management）の初期フェーズである  
「OSINT による外部露出資産の発見」を体系的かつ実践的にまとめる。  
MITRE ATT&CK「Reconnaissance」、OWASP ASVS V1 に対応する。

---

## 1. 目的

OSINT 初期発見の目的は以下の 3 点である。

- 公開情報のみで外部露出資産を把握する  
- 技術スタックを推定する  
- 潜在的な侵入口を抽出する  

ASM の自動化領域であり、ペネトレーションテストにおける Recon の基盤となる。

---

## 2. 情報源の体系

### 2.1 DNS 情報
- A / AAAA：接続先サーバ  
- MX：メール基盤の判定（O365 / Google Workspace）  
- TXT：SPF / DKIM / DMARC  
- CNAME：CDN / クラウド推定  

---

### 2.2 CT（Certificate Transparency）ログ
- 過去に発行された証明書の一覧  
- 内部サービス名が混入するケースがある  

---

### 2.3 ASN / IP レンジ
- 組織が所有する IP ブロックの特定  
- IP に紐づく公開サービスの確認  

---

### 2.4 インターネットスキャン（Shodan / Censys）
- 露出ポート  
- ミドルウェア  
- VPN / RDP / メールサーバの種類  
- TLS 情報  

---

### 2.5 クラウド露出調査
- AWS S3 / Azure Blob / GCP Storage の公開可否  
- CloudFront / Cloudflare の構成推定  

---

### 2.6 SaaS / CI/CD
- GitHub（コード・issue・pages）  
- Notion / Confluence 公開スペース  
- CI/CD 公開ログ  

---

## 3. 実践手順
---

### 3.1 DNS の確認（dig）
~~~~
dig example.com ANY
dig MX example.com
dig TXT example.com
dig CNAME www.example.com
~~~~

---

### 3.2 CT ログ確認（crt.sh）
アクセス：  
https://crt.sh/?q=example.com

確認項目：  
- サブドメイン  
- サービス名  
- ワイルドカード証明書  

---

### 3.3 Shodan / Censys 参照
~~~~
shodan search "ssl.cert.subject.cn:example.com"
shodan search "org:ExampleCorp"
~~~~

---

### 3.4 ASN / IP レンジ調査
~~~~
whois example.com
whois ASXXXX
~~~~

---

### 3.5 クラウド露出調査（存在確認のみ）
~~~~
https://<bucket-name>.s3.amazonaws.com/
https://storage.googleapis.com/<bucket-name>/
~~~~

※ 公開設定の有無（403/404 等）の確認のみ行い、攻撃的操作は禁止。

---

## 4. 注意点（Risk / Restrictions）

- スキャン、認証試行、ブルートフォースは禁止  
- 公開情報（OSINT）の範囲に限定して調査する  
- 取得した情報の扱いには注意する  

---

## 5. 参考文献（References）

- MITRE ATT&CK: Reconnaissance  
- OWASP ASVS V1 Architectural Requirements  
- NIST SP800-115 Technical Guide to Security Testing  
- NIST SP800-53 RA-5 Vulnerability Monitoring  
- ISO/IEC 27002 5.36 公開サービスの管理  