
# STEP1-3 サブドメイン列挙（Subdomain Enumeration）

本ドキュメントは ASM（Attack Surface Management）および  
Recon フェーズにおける「サブドメイン列挙」を体系化し、  
実務で利用可能な調査手順としてまとめる。

---

## 1. 目的

サブドメイン列挙の目的は以下である。

- 組織の外部公開資産をより正確に把握する  
- 未管理・放置サブドメインを発見する  
- クラウド / CDN / SaaS 利用状況を把握する  
- 侵入口となり得る「古い環境」「テスト環境」を抽出する  

ASM の基盤となる工程であり、攻撃者が最初に行う調査と同じである。

---

## 2. 列挙手法の体系

### 2.1 Passive Enumeration（非侵襲型）
対象へトラフィックを送らない手法。

- Certificate Transparency（crt.sh 等）
- 既知のサブドメインDB（RapidDNS / BufferOverrun）
- DNS キャッシュ / Passive DNS
- 検索エンジン（Google, Bing 等）

特徴：  
安全・企業向けの ASM フローで最優先。

---

### 2.2 Active Enumeration（DNS クエリ型）
DNS を問い合わせてサブドメインを列挙する手法。

- `dig` を用いたレコード参照  
- DNS ワードリストによる列挙（dev / stage / api / mobile 等）
- ワイルドカードの有無を確認

特徴：  
一般的な DNS クエリは許容されやすいが、  
短時間に大量クエリを送る形は避ける（VDP では特に注意）。

---

### 2.3 Permutation / Mutation（派生名生成）
既知のサブドメインをもとに派生名を生成する。

例：  
- `api.example.com` → `v2-api.example.com`  
- `dev.example.com` → `dev1.example.com`  

ASM 製品でも自動化されている代表手法。

---

### 2.4 Service-based Enumeration
CDN / クラウドなどのサービス名から逆引きする。

例：  
- CloudFront のドメイン → 対応するサブドメインを推測  
- Azure WebApps の規則 → `<name>.azurewebsites.net` から本番環境を特定  
- SaaS（Auth0 / Okta）のテナント名 → SSO サブドメイン推定  

---

## 3. 実践手順（What to do）

---

### 3.1 CT ログからの列挙（Passive / 推奨）

~~~~
https://crt.sh/?q=example.com
~~~~

確認内容：

- 古い・未使用サブドメインの発見  
- 開発環境（dev / stage）  
- SSO / VPN / API / CDN 構成  
- wildcard 証明書の存在  

---

### 3.2 DNS データベースから列挙（Passive）

~~~~
https://rapiddns.io/subdomain/example.com
https://dns.bufferover.run/dns?q=example.com
~~~~

確認内容：

- 過去に公開されていた資産  
- 異なるリージョン・別環境  
- 過去のメール・管理サービス  

---

### 3.3 dig による DNS レコード確認（Active）

~~~~
dig A sub.example.com
dig CNAME sub.example.com
dig ANY example.com
~~~~

確認内容：

- CNAME → CDN / クラウド判定  
- IP → ホスティング先の推定  
- ANY → 過去設定の痕跡  

---

### 3.4 Subfinder による列挙（Passive + 一部 Active）

~~~~
subfinder -d example.com -o result.txt
~~~~

確認内容：

- Passive DB からの網羅的サブドメイン収集  
- Active DNS への問い合わせも実施される場合あり  

注意：  
→ 1 秒間に大量の DNS クエリを送らない設定で実行すること。

---

### 3.5 Amass による広範探索（Passive 推奨設定）

~~~~
amass enum -passive -d example.com -o amass_result.txt
~~~~

確認内容：

- Passive ソースからの深い探索  
- ASN / netblock 情報との紐付け  
- 過去の資産発見能力が高い  

---

### 3.6 Permutation（派生生成）

ツール例：dnsgen

~~~~
cat result.txt | dnsgen - > mutate.txt
~~~~

確認内容：

- 未登録だが存在可能性の高いサブドメイン候補  
- ASM 製品が自動生成する “likely patterns” と同等  

---

### 3.7 クラウドサービスからの逆引き（Service-based）

S3 バケット存在確認：

~~~~
https://<project>-prod.s3.amazonaws.com/
~~~~

CloudFront の CNAME 調査：

~~~~
dig CNAME assets.example.com
~~~~

Azure WebApps 推定：

~~~~
https://<name>.azurewebsites.net
~~~~

確認内容：

- 本番 / 開発環境の分離状況  
- クラウド構成から推測される内部名  
- 古い環境・放置資産  

---

## 4. 注意事項（必ず遵守）

- DoS 的な DNS ブルートフォースは禁止  
- スキャン・認証試行・脆弱性利用は禁止  
- アクティブ操作は DNS クエリに限る  
- 取得情報は社内ルールに従って適切に保管  
- VDP の範囲外に対する調査は禁止  

---

## 5. 参考文献

- MITRE ATT&CK: Reconnaissance  
- OWASP ASVS V1 Architectural Requirements  
- NIST SP800-115  
- NIST SP800-53 RA-5  
- ISO/IEC 27002 5.36  