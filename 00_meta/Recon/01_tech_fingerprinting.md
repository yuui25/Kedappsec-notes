# 01_Tech_Fingerprinting (L3 Detailed Manual)

本ドキュメントは、L3 Blackbox Recon の最初のフェーズである  
**Tech Fingerprinting** を再現性高く実施するための “実務手順” をまとめたもの。

---

# 目的
- ベース技術スタックの把握  
- BAC / Logic Flaw 推論に必要な「認可の境界」候補を抽出  
- JS/API解析の事前準備  
- Cloud/SaaS 依存性の特定  

---

# 手順一覧（Overview）
1. 入口アクセス & HTTPヘッダ確認  
2. HTTPレスポンスから Hosting / CDN / WAF 推定  
3. HTML / JS から Framework / Runtime 推定  
4. SPA / SSR 判定  
5. Token Storage 判定（Cookie/Local/Session）  
6. Session Lifecycle の基本動作確認  
7. CSP / CORS 解析（外部依存の抽出）  
8. Build artifact 確認（asset / static / map）  
9. Feature flag / Config JS 調査  
10. OAuth/OIDC hint 抽出（/authorize / .well-known）  

---

# 詳細手順

---

## 1. 入口アクセス & HTTPヘッダ確認
### 手順
1. ブラウザ or curl で対象URLを開く  
2. 最初のレスポンス（HTML / redirect）を Burp or VEX で取得  
3. 以下ヘッダを抽出：

### 抽出する項目
- Server  
- X-Powered-By  
- Via / X-Cache  
- CF-Cache-Status / Fastly-Status  
- Strict-Transport-Security  
- Set-Cookie（Secure / HttpOnly / SameSite / prefix）  

### 目的
- Hosting/CDN/WAF の推定  
- アプリのバージョン情報  
- 認証方式の初期仮説を立てる  

---

## 2. Hosting / CDN / WAF 推定
### 手順
1. HTTPヘッダ・TLS証明書・処理遅延の特徴を見る  
2. 特徴例：

### CDN 判定
- Cloudflare: cf-ray, cf-cache-status  
- Fastly: x-served-by, x-timer  
- Akamai: x-akamai-transformed, x-ghost  

### Hosting 判定
- AWS: CloudFront, ELB, S3  
- Vercel: server=Vercel, x-vercel-id  
- Netlify: server=Netlify  
- Heroku: via=1.1 vegur  

### WAF 判定
- Akamai Bot Manager  
- Cloudflare WAF  
- Imperva  
- AWS WAF  

### 目的
- Subdomain takeover の可能性  
- Cloud misconfig の候補  
- API gateway の推定（後続 API mapping に必要）  

---

## 3. HTML / JS から Framework / Runtime 推定
### 手順
1. HTML 内の script タグを確認  
2. JS ファイルの命名規則・コメント・ビルド方式を判定  
3. 特徴的な構造からフレームワーク推定：

### 特定例
- React / Next.js: chunks, _next/static  
- Vue / Nuxt: _nuxt/, app.js, vendor.js  
- Angular: main.js, polyfills.js  
- Rails: authenticity_token  
- Laravel: mix-manifest.json  
- Spring: JSESSIONID / JWK の存在  

### 目的
- JS/APIの構造推測  
- SSR/SPApp の分岐  
- CSRF / Token 方式推定  

---

## 4. SPA / SSR 判定
### SPA の特徴
- index.html のみ  
- 大量の chunk.js  
- 画面遷移は XHR/fetch のみ  

### SSR の特徴
- ページ遷移ごとに HTML が返る  
- ルータ構造がURLで完結  

### 手順
1. 3–5画面遷移して XHR の比率を見る  
2. HTMLレスポンスの差異を見る  

### 目的
- API フローの形を推測  
- JS/API mapping の難易度を決める  

---

## 5. Token Storage 判定（Cookie/Local/Session）
### 手順
1. DevTools → Application → Storage を確認  
2. JWT が local/sessionStorage にあるか  
3. Cookie の Secure/HttpOnly を確認  

### 目的
- 認証方式の推測  
- Token refresh の有無  
- Session fixation / XSS 影響範囲の推定  

---

## 6. Session Lifecycle の基本動作
### 手順
1. ログイン  
2. 新しいタブを開く  
3. 再ログイン  
4. refresh → token 変化を観察  

### 観点
- Rotation の有無  
- Expire方式  
- BackendとFrontendの同期  

### 目的
- Flow bypass の候補探索  
- Token misuse の可能性  

---

## 7. CSP / CORS 解析（外部依存の抽出）
### 手順
1. レスポンスヘッダから CSP を取得  
2. domain-list を抽出（CSP Recon）  
3. 外部JS/CSS/CDN を列挙  

### 目的
- 外部依存（潜在サプライチェーン）の特定  
- Cloud/SaaS misconfig 予測  

---

## 8. Build artifact 調査（map/static/assets）
### 手順
1. /static/, /assets/, /_next/, /_nuxt/ を探索  
2. map ファイルがあれば優先閲覧  

### 目的
- Source map から API 構造把握  
- hidden endpoint 予測  

---

## 9. Feature flag / Config JS 調査
### 手順
1. env.js / settings.js / config.js を探す  
2. API endpoint や role 設定が露出してないか調べる  

---

## 10. OAuth/OIDC hint 抽出
### 手順
1. /authorize /token /logout /refresh  
2. /.well-known/openid-configuration  
3. scope / redirect_uri の扱い  

### 目的
- 認証・認可フローの境界推論  
- Token交換の隙間の検出  

---

# 出力テンプレ（Kedappsec-notes へ貼る用）
~~~~
## Tech Fingerprinting Summary
- Hosting/CDN/WAF:
- Framework/Runtime:
- SPA/SSR:
- Token Storage:
- Session Lifecycle:
- CSP/CORS:
- Build Artifacts:
- OAuth/OIDC:
- Notes:
~~~~

---