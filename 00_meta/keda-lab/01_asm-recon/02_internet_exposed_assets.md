# STEP1-2 インターネット露出資産（Internet-Exposed Assets）

本ドキュメントは、ASM（Attack Surface Management）の基礎となる  
「インターネット露出資産」の定義と分類を、事実ベースで整理する。

## 1. 定義

インターネット露出資産（Internet-Exposed Asset）とは、  
インターネット経由で直接アクセス可能なネットワーク資産を指す。  
認証の有無は問わず、以下のいずれかを満たすものを含む。

- インターネットから到達可能な IP / FQDN を持つ  
- 公開 DNS レコードにより外部から名称が参照可能  
- 公開 HTTP(S) エンドポイントを有する  
- 公開クラウドリソースを含む（ストレージ、CDN、API Gateway 等）

これは **MITRE ATT&CK「Reconnaissance」** における  
「Identify Network Assets」「Search Target Infrastructure」に対応する。

## 2. 主な種類

### 2.1 ドメイン / サブドメイン
- 企業所有の apex ドメイン  
- サービス、社内基盤、検証環境などに紐づくサブドメイン  
- 歴史的に残った “ゾンビドメイン” も攻撃対象となる

### 2.2 ネットワークサービス
- Web (HTTP/HTTPS)
- SMTP / IMAP / POP3
- SSH / RDP / VPN / VDI
- API エンドポイント（REST / GraphQL）

### 2.3 認証基盤関連
- SSO ポータル（Azure AD / Okta / IdP）
- VPN ポータル（SSL-VPN 等）
- OWA / ECP / メール WebUI
- MFA チャレンジ用エンドポイント

### 2.4 クラウド公開リソース
- AWS S3（public-read / website-hosting）  
- Azure Blob  
- Google Cloud Storage  
- CloudFront / Cloudflare など CDN  
- API Gateway / Functions / Web Apps

### 2.5 SaaS による外部公開範囲
- GitHub Pages / GitLab Pages  
- Confluence / Notion の公開スペース  
- CI/CD ログの外部公開設定  
- プロジェクト管理ツールの公開ビュー

### 2.6 古い・未管理資産（Shadow / Zombie Asset）
- 過去プロジェクトのドメイン  
- 退職者が管理していた SaaS  
- 役割不明のクラウドリソース  
- 利用されていない CDN / バケット

## 3. これらが ASM で重要とされる理由

1. 攻撃者は組織が **把握していない露出資産** から侵入する傾向が強い  
2. バグバウンティでも最初に狙われるのは “未管理ドメイン” である  
3. クラウド公開設定ミス・CDN構成ミスは増加している  
4. 露出資産の棚卸しは OWASP SAMM・NIST SP800-53 でも推奨される

## 4. 実践で行う最初のステップ（概要）
※ ここでは手順の概要のみを示し、詳細は別ファイルで扱う。

- DNS レコードの確認  
- CT（Certificate Transparency）ログの参照  
- ASN / IP レンジの特定  
- サブドメイン列挙  
- クラウドリソースの公開状況チェック  
- SaaS の公開ページ確認  
- GitHub 漏洩チェック

上記を組み合わせて、  
「外部から見える企業の“攻撃対象領域”」を初期把握する。