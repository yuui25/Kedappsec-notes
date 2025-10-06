# 05_PayloadsAllTheThings — Webペネトレーション開始要件と使い方（Kedappsec-notes）

**対象**: Webペネトレーションテスト（WebPT）で **PayloadsAllTheThings（PATT）** を安全かつ効果的に活用するための要件・手順・品質ルールをまとめる。  
**想定読者**: WebPT担当者／レビュー担当／報告書作成者。

---

## 1. 位置づけ（何者か・どう使うか）

- **PATTの立ち位置**: 実務に役立つ *ペイロード＆バイパス* のカタログ。探索済みの攻撃観点（WSTG 等）に対して、**試験入力（payload）を素早く準備**するためのリファレンス。  
  - 公式: <https://swisskyrepo.github.io/PayloadsAllTheThings/> ／ GitHub: <https://github.com/swisskyrepo/PayloadsAllTheThings>
- **使い分け（本リポ内の他参照と）**  
  - **WSTG** …「何をどう検証するか（観点／手順）」を決める。  
  - **PATT** …決まった観点に対して「**何を入れるか（payload）**」を即用意する。  
  - **ASVS** …深度・受入基準（DoD）を定義。  
  - **ATT&CK** …成立した攻撃を行動様式でマッピング（連鎖の説明）。

> **重要**: PATTは**一次資料（OWASP/WSTG・標準・ベンダー公式等）で裏取り**して使う。DoS/破壊系は書面合意なしに試さない（PATTの **DISCLAIMER** 参照）。

---

## 2. Web脆弱性診断との明確な違い（根拠つき）

- **Web脆弱性診断（Vulnerability Scanning/Assessment）**  
  - 目的: 既知の弱点を**幅広く検出・棚卸**する。自動化重視。  
  - 手段: ネットワーク／アプリの**スキャン**で既知脆弱性やミス設定を特定。結果解釈が必要。  
  - 根拠: **NIST SP 800-115** 第4章「Vulnerability Scanning」— スキャンはホスト属性と既知脆弱性の識別に有効（アウトデートソフト・パッチ有無・誤設定の把握）と記載（pp. 26–28）。

- **Webペネトレーションテスト（Penetration Testing）**  
  - 目的: **実攻撃**を模倣し、**実際に侵害が成立するか**と**影響範囲**を示す（防御検知能力も含む）。  
  - 手段: 攻撃者の手口・ツールを用い、**複数脆弱性の組み合わせ**で横展開や権限昇格を狙う。計画・通知・安全対策が必須。  
  - 根拠: **NIST SP 800-115** 第5.2章「Penetration Testing」— “実世界の攻撃を模倣し、セキュリティ機能の迂回方法を特定する。**実システム・実データに対する実攻撃**を含み得るため**リスクが高い**” 等（p.35–39）。

- **補足**: **WSTG** は Webテストの枠組み（観点と手順）を提供し、**PTES** は事前合意・スコープ定義等の**事前準備**を重視（WSTG「Penetration Testing Methodologies」からの参照、PTES Pre-engagement）。
  - WSTG 最新: <https://owasp.org/www-project-web-security-testing-guide/>  
  - WSTG「Penetration Testing Methodologies」: <https://owasp.org/www-project-web-security-testing-guide/latest/3-The_OWASP_Testing_Framework/1-Penetration_Testing_Methodologies>  
  - PTES Pre-engagement: <https://www.pentest-standard.org/index.php/Pre-engagement>  
  - NIST SP 800-115: PDF <https://nvlpubs.nist.gov/nistpubs/legacy/sp/nistspecialpublication800-115.pdf>

---

## 3. 前提・禁止事項（安全・法令遵守）

- **明示的許可のない試験禁止**（PATT **DISCLAIMER**）: 本資料は**教育・研究目的**。**適法かつ権限のある環境のみ**で利用すること。  
  - PATT DISCLAIMER: <https://swisskyrepo.github.io/PayloadsAllTheThings/DISCLAIMER/>
- **リスク低減策（NIST SP 800-115）**: 熟練テスター／包括的計画／活動ログ／**営業時間外**の実施／**本番の複製（ステージング）**での実験等を推奨（ES-2）。  
- **ルール・オブ・エンゲージメント（RoE）**: NIST 付録Bの雛形を参照。**禁止系**（DoS・大量送信・破壊／データ流出）と**停止条件**を明文化。

---

## 4. 開始要件（Definition of Ready / DoR）

1) **事前合意（PTES準拠）**  
   - スコープ：機能/画面/API、テナント、対象環境（優先は**ステージング**）、期間・時間帯、禁止事項。  
   - アカウント：ロール別（一般/特権/ゲスト）、MFA方針、テストデータ。  
   - 可用性配慮：レート制限／ジョブ/バッチ時間帯、監視・連絡体制、**緊急停止連絡先**。

2) **証跡・再現性**  
   - 収集物：**HTTP Req/Res（時刻・トレースID）**、スクリーンショット、Burpプロジェクト、サーバログ抜粋。  
   - 論点整理：**成立条件／再現手順／影響評価**、既存防御の検知状況。

3) **品質と安全**  
   - **入力生成は段階的**（低リスク→高リスク）。WAF/RateLimit観測。  
   - 失敗時ロールバック手順（データ汚染・キュー滞留・キャッシュ汚染の復旧）。

---

## 5. 具体的な使い方（最短ハンズオン）

### 5.1 典型フロー
1. **観点を決める（WSTG）**：例）Input Validation（HPP/SQLi/XSS）や SSRF 等。  
   - 例: WSTG 4.7 Input Validation Testing → <https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/README>
2. **PATTでペイロード収集**：対象技術のページを選び、**最小→強力**の順で準備。  
   - 例: HPP, SQL Injection, SSRF, Directory Traversal, Encoding & Transformations。  
3. **Burp Intruder で実行**（Sniper/Cluster Bomb などを選択）。  
   - Intruder概要: <https://portswigger.net/burp/documentation/desktop/tools/intruder>  
   - 使い始め: <https://portswigger.net/burp/documentation/desktop/tools/intruder/getting-started>  
   - 位置指定: <https://portswigger.net/burp/documentation/desktop/tools/intruder/configure-attack/positions>  
   - 攻撃タイプ: <https://portswigger.net/burp/documentation/desktop/tools/intruder/configure-attack/attack-types>
4. **観測とチューニング**：レスポンス差分・エラー兆候・リダイレクト・時間差を観測。検証できれば**tamper**（WAF回避／符号化変更）で再試行。  
5. **連鎖と説明**：成立したら**権限上げ・横展開**を検討し、**ATT&CK**で技術IDを付して報告。

### 5.2 ミニ・レシピ（例）

- **HPP（HTTP Parameter Pollution）**  
  - PATT: <https://swisskyrepo.github.io/PayloadsAllTheThings/HTTP%20Parameter%20Pollution/>  
  - WSTG観点: <https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/04-Testing_for_HTTP_Parameter_Pollution>  
  - 手順（概略）:  
    1) 影響しそうなパラメータを洗い出し（例：`role=`, `redirect=`）。  
    2) Intruder **Sniper**で同名パラメータの重複を注入（`?role=user&role=admin` など）。  
    3) **順序**・**位置**・**符号化**（URL/Unicode/双方向）を変化させ差分観測。

- **SQLi ログイン回避**  
  - PATT Intruder ワードリスト例：Auth_Bypass.txt  
    - <https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/Intruder/Auth_Bypass.txt>  
  - 手順（概略）:  
    1) ログインPOSTの`username`/`password`に位置指定。  
    2) **Auth_Bypass**リストを設定し Sniper で走査。  
    3) ステータスコード／長さ／Locationヘッダで成功判定。WAFで阻まれる場合は**tamper**（SQLmapのtamper参考）。

- **SSRF メタデータ検証**  
  - PATT: <https://swisskyrepo.github.io/PayloadsAllTheThings/Server%20Side%20Request%20Forgery/>  
  - 手順（概略）:  
    1) URL入力点を特定し、ローカル/クラウドメタデータ先（`169.254.169.254` 等）を段階的に試行。  
    2) 内部到達可否の**タイムアウト差**や**ヘッダ挙動**を観測。  
    3) 許可範囲内で**内部ポート探索**や**ファイル取得**に発展させる。

- **Directory Traversal × Encoding 組合せ**  
  - PATT: Traversal と **Encoding & Transformations**  
    - Traversal: <https://swisskyrepo.github.io/PayloadsAllTheThings/Directory%20Traversal/>  
    - Encoding: <https://swisskyrepo.github.io/PayloadsAllTheThings/Encoding%20and%20Transformations/>  
  - 手順（概略）: 二重URLエンコード、Unicode正規化、パス区切り多様化（`..%2f`, `%252e%252e%2f` 等）を順次適用。

> **運用Tips**: Intruderの**payload positions**を最小化し、**レート**を抑制。既知の**危険ペイロード**（大量サイズ・fork爆弾等）は**ステージング限定**。

---

## 6. 成果物と報告（DoD 例）
- **証跡**：Req/Res（時刻・相関ID）・スクショ・Burpプロジェクト・サーバログ抜粋。  
- **成立条件**：入力値・前提状態・権限・データ必要性。  
- **影響評価**：機微情報／認可逸脱／RCE 可能性／二次被害。  
- **防御状況**：WAF／RateLimit／監査ログの検知有無。  
- **再現手順**：手順書＋PATTの参照箇所（URL・見出し）。  
- **連鎖整理**：ATT&CKの戦術・技術ID付与。

---

## 7. 品質ルール（この文書の運用）
- **根拠の優先度**：OWASP（WSTG/ASVS）＞ NIST SP 800-115／PTES ＞ PortSwigger（Burp） ＞ PATT。  
- **非破壊の原則**：同意なきDoS/破壊・大量送信禁止。危険ペイロードは**ステージング限定**。  
- **更新**：PATTページの改訂に追随（特に DISCLAIMER／Methodology）。リンク死活監視。

---

## 参考（一次情報・公式・主要ページ）
- **PayloadsAllTheThings** 本体：<https://swisskyrepo.github.io/PayloadsAllTheThings/> ／ GitHub：<https://github.com/swisskyrepo/PayloadsAllTheThings>  
  - DISCLAIMER：<https://swisskyrepo.github.io/PayloadsAllTheThings/DISCLAIMER/>  
  - HPP：<https://swisskyrepo.github.io/PayloadsAllTheThings/HTTP%20Parameter%20Pollution/>  
  - SSRF：<https://swisskyrepo.github.io/PayloadsAllTheThings/Server%20Side%20Request%20Forgery/>  
  - SQLi（Intruder/リスト例）：<https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/Intruder/Auth_Bypass.txt>  
  - Encoding & Transformations：<https://swisskyrepo.github.io/PayloadsAllTheThings/Encoding%20and%20Transformations/>
- **OWASP WSTG**：<https://owasp.org/www-project-web-security-testing-guide/>  
  - Input Validation Testing：<https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/README>  
  - HPPテスト：<https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/04-Testing_for_HTTP_Parameter_Pollution>
- **NIST SP 800-115**（テスト計画・RoE・用語定義）：<https://nvlpubs.nist.gov/nistpubs/legacy/sp/nistspecialpublication800-115.pdf>  
- **PTES** Pre-engagement：<https://www.pentest-standard.org/index.php/Pre-engagement>  
- **PortSwigger（Burp Intruder）**：  
  - 概要：<https://portswigger.net/burp/documentation/desktop/tools/intruder>  
  - はじめに：<https://portswigger.net/burp/documentation/desktop/tools/intruder/getting-started>  
  - ポジション設定：<https://portswigger.net/burp/documentation/desktop/tools/intruder/configure-attack/positions>  
  - 攻撃タイプ：<https://portswigger.net/burp/documentation/desktop/tools/intruder/configure-attack/attack-types>

---

> **このファイルは Kedappsec-notes 専用の運用ガイド**です。Webペネトレサービス化フォルダの運用方針とは別管理です。
