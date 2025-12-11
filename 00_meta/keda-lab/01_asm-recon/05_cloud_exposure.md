<<<BEGIN>>>
# STEP1-5 クラウド露出資産の調査（Cloud Exposure Identification）

本ドキュメントは、インターネット上に公開され得る  
クラウド資産（S3 / Azure Blob / GCP Storage / CDN など）を  
非侵襲的に確認するための体系と実践手順をまとめる。

---

## 1. 目的

クラウド露出資産の調査目的は以下である。

- インターネット上に誤って公開されたクラウドリソースを特定する  
- CDN の挙動から背後の Origin 構成を推定する  
- クラウド命名規則からプロジェクト構成・環境名を推測する  
- 公開設定の可能性があるストレージを把握する  

これは ASM が最も重要視するカテゴリの一つであり、  
実攻撃においても初動で必ず調査される領域である。

---

## 2. 調査対象の体系

### 2.1 ストレージ系（S3 / Azure Blob / GCP Storage）
特徴：
- URL が規則的で予測しやすい  
- 誤公開（public-read）が頻発  
- プロジェクト名・環境名がそのままバケット名になることが多い  

形式例：  
- AWS: `https://<bucket>.s3.amazonaws.com/`  
- Azure: `https://<account>.blob.core.windows.net/<container>`  
- GCP: `https://storage.googleapis.com/<bucket>`  

---

### 2.2 CDN（CloudFront / Cloudflare / Fastly / Akamai）
特徴：  
- CDN 固有ヘッダで判別可能  
- CNAME によりサービスの背後構成が推測可能  
- キャッシュ挙動で公開範囲がわかる場合あり  

形式例：  
- CloudFront: `xxxxx.cloudfront.net`  
- Cloudflare: `CF-RAY`, `server: cloudflare`  
- Fastly: `x-served-by:`  

---

### 2.3 クラウドベースの Web サービス
例：  
- Azure WebApps  
- Firebase Hosting  
- Vercel / Netlify  
- API Gateway（AWS / GCP / Azure）  

URL パターンが規則化されており、  
外部から推測可能である。

---

### 2.4 名前規則（Naming Convention）による推測
組織によくある命名規則：

例：  
- `<company>-prod`  
- `<project>-stg`  
- `<team>-dev`  
- `<service>-cdn`  

命名規則により、存在し得るクラウド資産を推測可能。

---

## 3. 実践手順（各体系に対応）

### 3.1 S3 バケットの存在確認（2.1 に対応）

~~~~
https://<bucket>.s3.amazonaws.com/
~~~~

確認内容：
- 200 → 公開中  
- 403 → 存在するが非公開  
- 404 → 存在しない  
- XML エラーで内部構造がわかる場合もある  

※ 操作はあくまで「存在確認」に限定する。

---

### 3.2 Azure Blob の基本確認（2.1 に対応）

~~~~
https://<account>.blob.core.windows.net/
https://<account>.blob.core.windows.net/<container>/
~~~~

確認内容：
- コンテナ名の列挙可否  
- 公開設定か否か  
- URL 規則から内部プロジェクト名推測  

---

### 3.3 GCP Cloud Storage（2.1 に対応）

~~~~
https://storage.googleapis.com/<bucket>
~~~~

確認内容：
- バケット存在  
- 公開範囲（PUBLIC/BLOCKED）  
- バケット名 → プロジェクト名・チーム名の手がかり  

---

### 3.4 CloudFront のヘッダ確認（2.2 に対応）

~~~~
curl -I https://assets.example.com
~~~~

確認内容：
- `X-Amz-Cf-Id` の有無 → CloudFront 経由  
- キャッシュ HIT/MISS  
- CNAME から Origin を推測（例：S3 / API Gateway）  

---

### 3.5 Cloudflare の識別（2.2 に対応）

~~~~
curl -I https://www.example.com
~~~~

確認内容：
- `CF-RAY` → Cloudflare  
- `server: cloudflare`  
- キャッシュ挙動  

---

### 3.6 Akamai / Fastly の識別（2.2 に対応）

Akamai:
~~~~
curl -I https://example.com
~~~~
確認内容：  
- `X-Akamai-Transformed` → Akamai 経由  

Fastly:
~~~~
curl -I https://example.com
~~~~
確認内容：  
- `x-served-by:` → Fastly  

---

### 3.7 クラウド Web サービスの判定（2.3 に対応）

Azure WebApps 例：
~~~~
https://<name>.azurewebsites.net
~~~~

Firebase Hosting 例：
~~~~
https://<project>.web.app
~~~~

Vercel 例：
~~~~
https://<project>.vercel.app
~~~~

確認内容：  
- 開発/ステージング/本番の環境分離  
- サービスの種類  
- プロジェクト構成の推測  

---

### 3.8 名前規則からの推測（2.4 に対応）

以下のような命名規則を元に「存在し得る」資産を列挙する。

例：  
- `<company>-prod`  
- `<company>-stg`  
- `<project>-dev`  
- `<project>-assets`  

確認内容：  
- クラウドの環境数  
- プロジェクト名の構造  
- チーム単位の初期推測  

---

## 4. 注意事項

- 存在確認以外の操作（PUT/DELETE 等）は行わない  
- 公開リソースであっても内部データの取得を行わない  
- CDN / クラウドの正常利用を妨げる大量リクエストを送らない  
- 情報の扱いには十分注意する  

---

## 5. 参考文献

- MITRE ATT&CK: Reconnaissance  
- AWS / Azure / GCP 各公式ドキュメント  
- OWASP ASVS V1  
- NIST SP800-115  

<<<END>>>