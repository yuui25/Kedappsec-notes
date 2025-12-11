# STEP1-2 OSINT 初期発見（Initial Discovery）

本ドキュメントは、ASM（Attack Surface Management）の基礎工程である  
「攻撃者が最初に実施する OSINT（Open Source Intelligence）」を体系的に整理する。  
MITRE ATT&CK の Reconnaissance、OWASP ASVS の情報収集フェーズに対応する。

---

## 1. OSINT 初期発見の目的

攻撃者が最初に行うのは、  
**「対象組織がインターネットに露出している情報を、公開データのみで把握する」**  
ことである。

目的は以下の 3 点に集約される。

1. 組織に紐づくドメイン・IP・クラウド・SaaS を列挙する  
2. 使用している技術スタック（WAF / CDN / 言語 / サーバ）を把握する  
3. 侵入口（認証基盤・古い環境・誤公開リソース）を抽出する

ASM ではこの初期発見を **自動化・継続化** する。

---

## 2. 主要 OSINT 情報源

攻撃者は公開情報だけで以下を確認できる。

### 2.1 DNS 情報（A / AAAA / CNAME / MX / TXT）
- ドメイン → どのサーバを指しているか  
- メール基盤（MX）→ 利用している SaaS（O365 / Google Workspace）  
- TXT → SPF / DKIM / DMARC から送信基盤を推測  
- CNAME → CloudFront・Azure・GCP など利用クラウドの判別

**技術的根拠：**  
DNS は公開データであり、制限なく参照可能。

---

### 2.2 Certificate Transparency（CT）ログ
- 発行された TLS 証明書の全履歴（ドメイン / サブドメイン）を確認できる  
- 内部向けのサブドメインが誤って含まれるケースがある  
- SaaS 利用も証明書名から判明する場合あり

**利用例：**  
- `*.example.com` の証明書 → 多数のサービス存在を推測  
- `vpn.example.com` の証明書 → VPN ポータルの存在を特定

---

### 2.3 ASN / IP レンジ調査
- 組織が所有する IP ブロックを特定  
- そこに紐づく Web サービスを関連付け

**例：**  
- 大企業は自社 ASN を持つ  
- 中小企業はクラウド上に点在するためクラウド ASN から逆推測

---

### 2.4 インターネットスキャンプラットフォーム
- **Shodan / Censys / ZoomEye**  
  → 露出ポート、ミドルウェア、バナー、WAF、証明書を取得

**抽出できる情報：**
- Apache / Nginx / IIS のバージョン  
- 古い OpenSSH / RDP の存在  
- VPN 製品名（PulseSecure / Fortinet / Citrix）  
- 認証不要の API やステータスページ

---

### 2.5 クラウド公開設定の調査
- AWS S3 / Azure Blob / GCP Storage の公開状態  
- CloudFront / Cloudflare など CDN の挙動

**推測できる内容：**
- バケット名規則 → プロジェクト名の推測  
- CDN → Origin サーバの存在（後続フェーズで扱う）

---

### 2.6 SaaS / CI/CD / ソースコード関連
- GitHub の公開リポジトリ  
- issue / wiki / pages の公開設定  
- CI/CD の公開ログ  
- Notion / Confluence の公開スペース

**典型的に判明する情報：**
- API キー  
- サーバ構成  
- 隠しエンドポイント  
- 使用フレームワーク

---

## 3. 初期発見の手法（技術別一覧）

以下は ASM で一般化されている “初期列挙手法” の体系化。

### 3.1 ドメイン起点の列挙
1. DNS レコード取得  
2. CT ログから追加サブドメイン発見  
3. 逆引き DNS で IP → FQDN の関係性を把握  
4. 露出 IP から Web ポート調査  
5. ポートの種類からサービス推定

---

### 3.2 SaaS / メール基盤の識別
- MX / SPF / DKIM から送信基盤（Microsoft / Google / SendGrid 等）を判定  
- CDN（Akamai / Cloudflare）を CNAME から識別  
- IdP（AzureAD / Okta）は証明書名やリダイレクトから判別

---

### 3.3 クラウド露出資産の発見
- バケット名推測（会社名・プロジェクト名・イニシャル）  
- 403 / 404 / 301 の振る舞いから存在判定  
- CloudFront の X-Amz-Cf-Id、サーバヘッダ等から構成推測

---

### 3.4 技術スタックの推測
- HTTP Response Header  
- WAF 判別（Akamai / F5 / Imperva など）  
- Web アプリの生成物（`_next/` `wp-` `laravel_session`）  
- フロントの JS / Source Map の構造からフレームワーク推定

---

## 4. OSINT 初期発見の“最終アウトプット”

ASM における OSINT 初期発見の成果物は以下。

1. **インターネット露出資産リスト（Inventory）**  
2. **技術スタック一覧（Tech Profile）**  
3. **潜在リスク候補（Pre-Risk List）**

このアウトプットを基に  
次段階の「サブドメイン列挙」「クラウド露出分析」「GitHubリーク調査」へ進む。

---

## 5. 標準参照（信頼できるソース）

- MITRE ATT&CK Reconnaissance  
- OWASP ASVS V1 Architectural Requirements  
- NIST SP 800-115（Technical Guide to Security Testing）  
- NIST SP 800-53 RA-5（Vulnerability Monitoring）  
- ISO/IEC 27002 5.36（公開サービスの管理）

これらはいずれも **「外部公開資産を把握すること」** を推奨している。