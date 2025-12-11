
# STEP1-4 技術スタック推定（Tech Stack Identification）

本ドキュメントは、外部公開サービスの「技術スタック（WAF / CDN / Webサーバ / フレームワーク等）」を  
非侵襲的に推定するための体系と手順をまとめる。

---

## 1. 目的

技術スタック推定の目的は以下である。

- 外部公開サービスが利用している基盤技術を把握する  
- WAF / CDN の有無と種類を推定する  
- Web サーバ・プログラミング言語・フレームワークを特定する  
- どの領域にリスクが集中しているか判定する  

これは ASM のプロファイリング工程に相当し、  
攻撃経路の予測にも用いられる重要工程である。

---

## 2. 推定手法の体系

### 2.1 HTTP レスポンスヘッダ解析
サービス側が返すヘッダからシステム情報を読み取る。

例：  
- `Server: nginx` → Web サーバ  
- `X-Powered-By: PHP/7.4` → 言語・バージョン  
- `Via:` / `X-Cache:` → CDN 判別  
- `X-Akamai-Transformed:` → Akamai  
- `CF-Ray:` → Cloudflare  

---

### 2.2 HTML・JavaScript からの推定
ページ内部の構造・スクリプトから使用技術を推定する。

例：  
- `_next/` → Next.js  
- `wp-content/` → WordPress  
- `static/js/main.*.js` → React  
- `window.__NUXT__` → Nuxt.js  
- `laravel_session` → Laravel  

---

### 2.3 TLS 証明書情報
証明書の発行者・SAN・共通名から判定する。

例：  
- `cloudflare` が issuer → Cloudflare 経由  
- SAN に多数のサブドメイン → ワイルドカード証明書利用  
- 証明書名に `<tenant>.azurewebsites.net` → Azure WebApps の可能性  

---

### 2.4 CDN / WAF の特徴的挙動
サービスごとに固有のレスポンス特徴がある。

例：  
- CloudFront → `X-Amz-Cf-Id`  
- Cloudflare → `CF-RAY`, `server: cloudflare`  
- Akamai → `X-Akamai-Transformed`  
- Imperva → 特徴的な cookie 名  
- Fastly → `x-served-by:`  

---

### 2.5 URL / パス構造
パスやファイル名のパターンから技術を推定する。

例：  
- `/api/v1/` → REST API  
- `/GraphQL` → GraphQL  
- `.aspx` → ASP.NET  
- `.jsp` → Java サーブレット  
- `.php` → PHP  
- `/wp-json/` → WordPress API  

---

## 3. 実践手順（各体系に対応）

### 3.1 HTTP レスポンスヘッダの確認（2.1 に対応）

~~~~
curl -I https://example.com
~~~~

確認内容：

- `Server:` で Web サーバ判定  
- `X-Powered-By:` で言語推定  
- CDN / キャッシュの有無  
- 特定製品固有ヘッダの有無  

---

### 3.2 HTML / JavaScript から推定（2.2 に対応）

ブラウザの開発者ツールで以下を確認する。

- HTML の構造  
- JS ファイルのパス規則  
- フレームワーク固有オブジェクト  
- クッキー名（例：`laravel_session` など）  

確認内容：

- CMS 利用（WordPress / Drupal 等）  
- フロントの種類（React / Vue / Next.js / Nuxt）  
- バンドル構造からの推測  

---

### 3.3 TLS 証明書確認（2.3 に対応）

~~~~
openssl s_client -connect example.com:443 -servername example.com
~~~~

確認内容：

- Issuer → CDN / クラウド / 自社運用か  
- SAN → 利用中のサブドメイン範囲  
- 証明書名 → SaaS の利用痕跡  

---

### 3.4 CDN / WAF のレスポンス確認（2.4 に対応）

~~~~
curl -I https://assets.example.com
~~~~

確認内容：

- CloudFront / Cloudflare / Akamai の識別  
- キャッシュの有無  
- CDN 背後に Origin があるかの推測  

---

### 3.5 URL / パス構造からの判定（2.5 に対応）

以下の項目を目視で確認する。

- API の有無（`/api/` `/v1/`）  
- ファイル拡張子（`.php` `.aspx` `.jsp`）  
- CMS 構造（`wp-content/` `wp-json/`）  
- 特徴的なアセットフォルダ  

確認内容：

- バックエンド言語  
- フレームワーク  
- CMS の種類  
- API スキーマ  

---

## 4. 注意事項

- サーバへ負荷をかける操作は避ける  
- 不要な大量リクエストを送らない  
- 判定はレスポンスや構造の「観察」に限定する  
- 内部構成を推測しても設定変更を試みてはならない  

---

## 5. 参考文献

- MITRE ATT&CK: Reconnaissance  
- OWASP ASVS V1  
- NIST SP800-115  
- ISO/IEC 27002:2022  
- Cloudflare, Akamai, AWS 各社の公式ドキュメント  