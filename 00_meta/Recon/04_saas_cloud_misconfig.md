# 04_SaaS_Cloud_Misconfig (L3 Detailed Manual)

本ドキュメントは、Web/App/API を対象とする L3 Blackbox Recon において  
**SaaS / Cloud インフラの誤設定を推定するための再現手順** を体系化したもの。

Hosting・CDN・Storage・Auth・Serverless・CI/CD の外部依存を把握し、  
誤設定・残骸・設定不備・権限境界の欠落を検出する。

---

# 目的
- Cloud / SaaS の依存を特定  
- 公開不要なアセット・API・ファイルの露出を発見  
- Subdomain takeover の候補抽出  
- Storage misconfig（S3, GCS, Azure Blob）推定  
- Web framework の serverless backend 推測  
- OAuth/OIDC / API Gateway の設定不備把握  
- バグバウンティで高確率当たる領域を体系的に実行  

---

# 手順一覧（Overview）
1. Hosting/CDN の確証取得  
2. Cloud provider 特有の挙動確認  
3. Subdomain → SaaS routing 不備の調査  
4. Static hosting（Vercel/Netlify/S3）の誤設定推定  
5. Storage（S3/GCS/Blob）公開設定の可能性を推定  
6. API Gateway / Serverless backend の探索  
7. OAuth/OIDC / IAM 周りの設定不備ヒント抽出  
8. External JS/CDN サプライチェーンリスク列挙  
9. CI/CD & public artifact の調査  
10. Cloud misconfig → BAC 合流ポイント推論  

---

# 詳細手順

---

## 1. Hosting/CDN の確証取得

Tech Fingerprinting で仮説は立てているため、ここで “確証” を得る。

### 手順
1. HTTPヘッダの provider 特有の識別子確認  
2. TLS 証明書の SAN に cloud provider 名が入っていないか  
3. Ping/trace route で routing の特徴から判定  

### 主な判別材料
- **Cloudflare**: cf-ray, cf-cache-status, cf-request-id  
- **Fastly**: x-served-by, x-timer  
- **Akamai**: x-akamai-transformed  
- **AWS CloudFront**: Via: CloudFront, X-Amz-Cf-Id  
- **Vercel**: x-vercel-id, server: Vercel  
- **Netlify**: server: Netlify  
- **Heroku**: via: 1.1 vegur  

### 目的
- その後の Cloud-specific misconfig に繋げる  

---

## 2. Cloud provider 特有の挙動を確認
### 確認項目
- デプロイ形式（serverless / edge / container）  
- 静的ファイルの配置先  
- API の routing method  
- SPA の routing（fallback index.html の存在）  
- Edge Function の有無  

Cloudを正しくモデル化できると  
**Cloud/SaaS 特有の misconfig の当たり方が変わる**。

---

## 3. Subdomain → SaaS routing 不備の調査（重要）
Subdomain takeover の最重要領域。

### 手順
1. CSP から外部ドメイン一覧を抽出  
2. CNAME 先の SaaS を確認  
3. SaaS 側の設定有無を推測  
4. 次で挙げるサービスを重点調査：

### よくある SaaS takeover 候補
- GitHub Pages  
- Netlify  
- Vercel  
- Heroku  
- AWS S3  
- Azure Static Web Apps  
- Google Firebase Hosting  

### 観点
- DNS が SaaS を指しているのに  
  **SaaS 側でプロジェクトが存在しない or 無効化されている**  
  → takeover リスク  

---

## 4. Static hosting（Vercel/Netlify/S3）の誤設定推定

### 手順（Vercel/Netlify）
1. /_assets, /_next/static, /_nuxt/ の公開状況を確認  
2. preview 環境の build artifactが露出していないか  
3. vercel.json / netlify.toml の想定挙動を推測  

### 手順（S3）
1. バケット名パターンを推測  
2. /assets/ /images/ /media/ の URL 構造から  
   **静的ファイルが S3 にあるか推論**  
3. public-read の可能性判断

### 目的
- Source map 露出  
- preview 環境露出  
- public bucket misconfig  

---

## 5. Storage（S3/GCS/Blob）公開設定の推定
Blackbox で “public-bucket確定” までは不要。可能性を推定する。

### 手順
1. static/media アセットの URL が cloud storage 特有か確認  
2. URL形式例：

#### S3
- https://xxx.s3.amazonaws.com/  
- https://bucket-name.s3.region.amazonaws.com/  

#### GCS
- https://storage.googleapis.com/bucket/  

#### Azure Blob
- https://xxx.blob.core.windows.net/container/  

### 観点
- public-read の可能性  
- privateでも signed URL に異常がないか  
- バケット名が外部に露出してないか  

---

## 6. API Gateway / Serverless backend の探索

### 手順
1. API path から判定  
   - /api/v1/** = API GW  
   - /api/**/functions = serverless function  
2. CORS 設定の挙動で推理  
3. エラーレスポンスに backend 種別が出る場合：

### 当たりやすい misconfig
- CORS: Access-Control-Allow-Origin: *  
- OPTIONS が緩い（認証不要）  
- 不要な dev function / preview API が生存  
- public function が role-check なし  

---

## 7. OAuth/OIDC / IAM 設定不備のヒント抽出

### 手順
1. /.well-known/openid-configuration を参照  
2. scope / response_type を確認  
3. token_endpoint の構造から backend の認可境界を推理  

### 当たりやすい misconfig
- ROPC が生存  
- redirect_uri がワイルドカード  
- client_id / client_secret 流出  
- scope が過剰に緩い  

---

## 8. External JS/CDN サプライチェーンリスク列挙

### 手順
1. CSP “script-src” / “connect-src” を抽出  
2. 外部CDNをすべて列挙  
3. 一般的危険域：

### よくあるリスクリスト
- jsdelivr  
- unpkg  
- cloudflare CDN  
- fastly CDN  
- googleapis  
- bootstrapcdn  

### 観点
- XSS → supply chain via CDN  
- hosted script takeover  
- 破棄されたCDN対象  

---

## 9. CI/CD & public artifact の調査

### 手順
1. /_next/static/, /_nuxt/ を確認  
2. sourcemap（.map）が残っていれば最優先で解析  
3. env.js / config.js で  
   **API KEY, Endpoint, Preview URL** が残っていないか確認  

### 当たりパターン
- preview 用 API endpoint がそのまま露出  
- feature flag の残骸  
- dev backend に自由アクセス可能  

---

## 10. Cloud misconfig → BAC 合流ポイント推論

Cloud misconfig は単体で脆弱性になるだけでなく  
**BAC（権限不備）に直結するケースが多い**。

### 合流シナリオ例
- SaaS 側の “preview admin API” が残存  
- dev/staging 環境の backend API が public  
- S3のJSON設定ファイルから role/permission が読み取れる  
- CloudFront の routing で admin path が公開されている  
- serverless function に role-check がない  

### 目的
- Cloud misconfig を “単体” で終わらせず  
  **権限不備の起点** として扱うため  

---

# 出力テンプレ（Kedappsec-notes 用）
~~~~
## SaaS / Cloud Misconfig Summary
- Hosting/CDN:
- Cloud Provider Guess:
- Subdomain Takeover Candidates:
- Static Hosting Behavior:
- Public Bucket Possibility:
- API Gateway / Serverless Behavior:
- OAuth/OIDC Misconfig Hints:
- External CDN / Supply Chain:
- CI/CD Artifacts:
- BAC Merge Points:
- Notes:
~~~~

---