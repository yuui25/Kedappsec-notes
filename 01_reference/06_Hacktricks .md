# HackTricksに基づく Webペネトレーション開始要件

> **位置づけ**：HackTricks は「実務テクニック集（入口探索・バイパス手法のカタログ）」として活用する。ASVS/WSTG/ATT&CK と対で使い、**根拠（一次資料）で裏取り**してから実施・報告する。

---

## 1. 立ち位置
- **HackTricks**：認可回避・ヘッダ悪用・周辺Tipsなど、**入口拡張／バイパス手法**の発想源。  
- **ASVS**：深度・受入れ基準（DoD）の**要件**決定。  
- **WSTG**：検証計画・観点の**手順**と証跡化ガイド。  
- **PayloadsAllTheThings**：HackTricksで得た当たりに対する**具体ペイロード**。  
- **ATT&CK**：成立した攻撃を**戦術/技術ID**で連鎖整理（報告の説明力向上）。

> 参考：README「役割と使い分け」「推奨参照フロー」に準拠。

---

## 2. Web脆弱性診断との明確な違い（根拠リンク付き）
**脆弱性診断（Vulnerability Assessment）**は潜在的弱点の**列挙**寄り（自動化比重高）で、  
**ペネトレーションテスト**は実際に**攻撃連鎖を成立**させ**影響を実証**する行為（手動比重高）。
- OWASP WSTG は**“テストは証拠に基づき弱点を検証”**する旨を定義（＝手順と証跡重視）。
- そのうえで**ペンテスト**は PCI DSS など外部基準にも準拠しつつ、**攻撃の成立と影響**を評価対象に含める。

**実務上の線引き（本ドキュメントでの扱い）**
- 診断：観点網羅＋弱点列挙（WSTGの観点と再現手順）。
- WebPT：HackTricks/PA‑TT を用い、**認可回避・迂回・連鎖**を試み、**ビジネス影響（機密閲覧／特権化／任意操作など）**を**証跡**で示す。

---

## 3. 着手前要件（Start Conditions / DoD）
**3.1 合意とスコープ**
- 許可文書：対象ドメイン/環境（本番 or ステージング）、禁止事項（DoS/大量送信/資源枯渇）、時間帯、窓口。
- 認証情報：アカウント種別（ロール）、APIキー/クライアント証明書などの扱い。
- 成果物：①サマリ ②詳細結果 ③再現手順 ④証跡（Req/Res・ログ・スクショ） ⑤改善提案。

**3.2 技術的前提**
- プロキシ/Burp（手動改変・ログ保存）、低侵襲モード（レート制御/インターバル）、安全なワードリスト管理。
- ステージングで**破壊的試験は先行検証**、本番は**合意内・最小限**のみ。

**3.3 DoD（受入れ基準）**
- 影響の**再現可能なPoC**と**証跡**が揃い、ASVS/WSTG/ATT&CKを**根拠付け**として引用。
- 誤検知/過度推測がないこと（一次資料リンクで裏取り済み）。

---

## 4. HackTricksの具体的な使い方（入口を広げ、連鎖させる）

> 最短フロー：**HackTricks で入口候補 → PA‑TTで投入値 → WSTG手順で検証 → 成立したら ATT&CK で連鎖可視化**。

### 4.1 入口探索（発想出し）
- **Web Vulnerabilities Methodology**（チェックリスト）で**見落とし削減**。  
  例：プロキシ層悪用（Hop-by-hop/Caching/Request Smuggling/H2C）、反射値の利用（CSTI/SSTI/SSRF/Prototype Pollution → XSS）。
- **403/401 Bypass**：パス正規化・大文字小文字・末尾スラッシュ・ヘッダ（`X-Original-URL` 等）・メソッドオーバーライドを素振り。
- **ヘッダ/ルーティング系**：Host Header Injection、CORS不備、HTTP Parameter Pollution（HPP）、HTTP Request Smuggling。

### 4.2 クイック試行（Burp/HTTP操作の型）
- **Host header**：`Host` 差し替え＋`X-Forwarded-Host` 併用→リダイレクト/リンク生成/キャッシュ挙動を観察。  
- **CORS**：`Origin` 任意/`null`、`Access-Control-Allow-*` の組合せを観察（資格情報付き）。  
- **HPP**：同名パラメータ二重化・配列化（`a=1&a=2` / `a[]=1&a[]=2` / JSONボディ重複）。  
- **Req Smuggling**：`CL`/`TE` 競合、`Transfer-Encoding` 変種（前段/後段の解釈差）。  
- **認可回避**：パス崩し、`..;`/末尾`/`、メソッド変更（`GET`→`POST`/`OPTIONS`）、ベアラトークン位置ずらし。

### 4.3 連鎖の作り方（例）
- **SSRF → クラウド/メタデータ → 認証情報抽出 → 内部面API操作**  
- **HPP → ACLバイパス／認可抜け**（バックエンドのマージ仕様差を突く）  
- **Host Header → リセットリンク奪取**／**キャッシュ汚染** → 認証フロー乗っ取り  
- **Req Smuggling → WAF前段すり抜け → 機微API直接叩き**

> それぞれ、**一次資料（下記リンク）で裏取り**してから証跡を取得。

---

## 5. 証跡と報告（品質ルール）
- すべての実験は **Req/Resの原文**、**ヘッダ/ボディ差分**、**時刻と対象**を紐付けて保存。
- 影響は**ビジネス文脈で定量化**（例：個人情報X件閲覧、不正送金の可能性、横展開難易度）。
- リスク評価は **OWASP Risk Rating** を基本とし、顧客スキーマ（CVSS/独自）に合わせて併記。
- **非破壊の原則**：可用性へ影響が出る操作は**事前合意**がある場合のみ、**時間帯/回数**を管理。

---

## 6. 参考リンク（根拠／一次資料中心）
### HackTricks（公式/本体・Web系）
- HackTricks Index（ローカル実行手順含む）：https://angelica.gitbook.io/hacktricks 
- Web Vulnerabilities Methodology（チェックリスト）：https://blog.1nf1n1ty.team/hacktricks/pentesting-web/web-vulnerabilities-methodology  
- 403/401 Bypass：https://angelica.gitbook.io/hacktricks/network-services-pentesting/pentesting-web/403-and-401-bypasses  
- Parameter Pollution（HPP）：https://angelica.gitbook.io/hacktricks/pentesting-web/parameter-pollution  

### OWASP / PortSwigger（裏取り・手順）
- WSTG 総論（最新/安定）：https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/00-Introduction_and_Objectives/README  
- Penetration Testing Methodologies（WSTG）：https://owasp.org/www-project-web-security-testing-guide/latest/3-The_OWASP_Testing_Framework/1-Penetration_Testing_Methodologies  
- Identify Application Entry Points（WSTG）：https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/06-Identify_Application_Entry_Points  
- OWASP Risk Rating：https://owasp.org/www-community/OWASP_Risk_Rating_Methodology  
- Host Header：https://portswigger.net/web-security/host-header/exploiting  
- CORS 基礎/誤設定：https://portswigger.net/web-security/cors  
- HTTP Request Smuggling：https://portswigger.net/web-security/request-smuggling/exploiting  

### SSRF（補助）
- SSRF 基礎（thehacker.recipes）：https://www.thehacker.recipes/web/inputs/ssrf/

> **注**：HackTricksの記事はミラー/翻訳が複数あるため、**リンク先の構成が変わる場合あり**。不達時はトップ（book.hacktricks.wiki）から検索。

---

## 7. 付録：実行テンプレ（Burp Repeater素振り）
```
# Host Header
GET /reset HTTP/1.1
Host: evil.example.com
X-Forwarded-Host: evil.example.com

# CORS（資格情報有）
GET /api/me HTTP/1.1
Origin: null
Cookie: session=...
# → レスポンスの ACAO/ACAC を確認

# HPP（同名パラメータ）
GET /admin?role=user&role=admin HTTP/1.1

# Request Smuggling（CL.TE 素振り——ステージング限定）
POST / HTTP/1.1
Transfer-Encoding: chunked
Content-Length: 4

0

GET /admin HTTP/1.1
Host: target
```
---

### ライセンス/運用ノート
- 本ファイルは Kedappsec-notes の**参考実装**であり、実施は常に**契約・合意書**に従うこと。
- 記載の技法は**教育・防御目的**の範囲でのみ使用し、第三者の権利を侵害しないこと。
