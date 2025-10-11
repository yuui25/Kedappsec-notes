# WSTGに基づく Webペネトレーション開始要件

**目的**：WSTG（OWASP Web Security Testing Guide）を一次資料として、**Webペネトレーションテスト（WebPT）**を開始・遂行・報告するための要件と具体的運用を示す。  

---

## 1. WSTG の位置付けと参照原則
- **WSTG とは**：Webアプリ/サービスのセキュリティ試験のための**標準ガイド**（観点・手順・テストケース群）。“Latest” 版は章立て「2. Introduction / 3. The OWASP Testing Framework / 4. Web Application Security Testing / 5. Reporting…」で構成される。  
  - 参考：WSTG Latest 目次 https://owasp.org/www-project-web-security-testing-guide/latest/
- **本リポジトリでの扱い**：WSTGは**検証観点と手順**の一次根拠。ASVS（要件）・ATT&CK（攻撃連鎖）と組み合わせ、根拠の優先度は *OWASP（ASVS/WSTG） > MITRE（ATT&CK） > そのほか* とする。

**用語**  
- **テストID表記**：WSTG の章・節・テスト名（例：`4.4.4 Bypassing Authentication Schema` / `4.5.4 IDOR` / `4.2.6 Test HTTP Methods` など）。  
- **DoD（Definition of Done）**：各作業の完了基準。本書で後述。

---

## 2. 「Web脆弱性診断」との違い（WSTG × WebPT）
- **脆弱性診断**：WSTG 等の観点に基づき**弱点の有無を識別**する活動。通常は**非破壊**かつ**個別観点の可否**に留める。  
- **WebPT**：識別に加え、**実際の悪用成立（Exploit）**や**攻撃連鎖（Privilege Escalation / 横展開 / 影響）**を**合意範囲内**で立証する活動。WSTG “Penetration Testing Methodologies（PTES準拠）” の 7 フェーズ（Pre-engagement / Intelligence Gathering / Threat Modeling / Vulnerability Analysis / Exploitation / Post-Exploitation / Reporting）を準用する。  
  - 参考：Penetration Testing Methodologies https://owasp.org/www-project-web-security-testing-guide/latest/3-The_OWASP_Testing_Framework/1-Penetration_Testing_Methodologies

> **要点**：WSTG は “観点と手順の一次資料”。WebPT はその観点を**連鎖させ、影響を実証**する。

---

## 3. WebPT 開始要件（合意事項 / 前提）
**(A) 前提合意（Pre-engagement / Rules of Engagement）**  
1) **目的**・**範囲（URL/機能/API/テナント）**・**期間/時間帯**  
2) **禁止事項**（DoS/大量送信/破壊系/本番データ改変）  
3) **アカウント**・**ロール**・**多要素認証(MFA)方針**（試験可否/手順）  
4) **ログ/監視**・**通知窓口**（緊急連絡/停止判断）  
5) **証跡**（Req/Res・スクショ・ログ保持方針）・**データ取り扱い**（機密度/削除期限）

**(B) 受入基準（DoD）**  
- 観点カバレッジ：`4.1〜4.12` の**適用カテゴリ**と**除外理由**を明記  
- 主要機能で**認証/認可/セッション/入力検証/ビジネスロジック**の**最低1つ以上の成立/不成立**を示す  
- 重大発見は**再現手順**・**最小破壊PoC**・**影響/前提/対策**・**WSTGテストID**の紐付けを報告  
- 終了判定：致命的リスクの**一時対処勧告**と**再試験方針**（必要に応じ）

---

## 4. 具体的な使い方（実務フロー）

### 4.1 計画（Testing Framework の当てはめ）
1) スコープ整理 → **対象資産/構成/データフロー**の“軽量脅威モデル”を作る  
2) **WSTG-4章**のカテゴリから**適用観点**を選定（下記 4.2）  
3) **前提合意**と**証跡ルール**を確定（§3）  
  - 参考：The Web Security Testing Framework（SDLC全体での適用）  
    https://owasp.org/www-project-web-security-testing-guide/latest/3-The_OWASP_Testing_Framework/0-The_Web_Security_Testing_Framework

### 4.2 観点選定（WSTG 4章の主カテゴリ）
- **4.1 情報収集（Information Gathering）**  
- **4.2 設計/デプロイ構成（Configuration & Deployment）**  
- **4.3 アイデンティティ管理（Identity Mgmt）**  
- **4.4 認証（Authentication）**  
- **4.5 認可（Authorization）**  
- **4.6 セッション管理（Session Mgmt）**  
- **4.7 入力検証（Input Validation）**  
- **4.8 例外処理（Error Handling）**  
- **4.9 弱い暗号（Weak Cryptography）**  
- **4.10 ビジネスロジック（Business Logic）**  
- **4.11 クライアントサイド（Client-side）**  
- **4.12 API**  
  - 参考：4章カテゴリ一覧 https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/

### 4.3 代表シナリオ（“観点→連鎖” の型）
- **(S1) 認証まわり**  
  - `4.4.4 Bypassing Authentication` を試行 → 失敗でも **`4.4.11 Testing Multi-Factor Authentication`** の**実装/回避**を確認  
  - 連鎖：弱いロックアウト（`4.4.3`） → **パスワードスプレー** → セッション固定/再利用（`4.6`）  
  - 参考（認証 目次）：https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/README  
  - 参考（MFA）：https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication
- **(S2) 認可（IDOR）**  
  - `4.5.4 Testing for Insecure Direct Object References`：リソースID/参照の**直接操作**と**アクセス制御**の実証  
  - 連鎖：閲覧→更新→削除／**テナント越境**→**水平/垂直特権昇格（4.5.3）**  
  - 参考（IDOR）：https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References
- **(S3) 入力検証→横展開**  
  - `4.7.4 Testing for HTTP Parameter Pollution`（同名パラメータ多重）→ サーバ側の**優先規則**悪用 → **認可/ACL回避**の検証  
  - `4.7.5 Testing for SQL Injection`：読み取り→更新→**認証回避**→**データ抽出**の最小PoC  
  - 参考（HPP）：https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/04-Testing_for_HTTP_Parameter_Pollution  
  - 参考（SQLi）：https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/05-Testing_for_SQL_Injection
- **(S4) 配置/運用の抜け穴**  
  - `4.2.6 Test HTTP Methods`（`OPTIONS`）で**許可メソッド**把握 → `PUT/DELETE/PATCH` 誤許可の**横展開**  
  - 古いバックアップ露出→**情報収集（4.1）**に**逆フィード**  
  - 参考（HTTP Methods）：https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/06-Test_HTTP_Methods
- **(S5) ビジネスロジック**  
  - `4.10`：**フロー逸脱**／**境界条件**／**手数料や割引計算**の**人間系エラー**を攻撃連鎖に接続  
  - 参考（Data Validation）：https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/01-Test_Business_Logic_Data_Validation

> **証跡化**：各シナリオで **(a) 前提** **(b) 試験手順** **(c) 期待/実結果** **(d) 影響** **(e) 推奨対策** を最小構成で記録し、**テストID**を紐付ける。

---

## 5. 成果物（レポート DoD）
- **エグゼクティブサマリ**（到達点：未認証/認証後／水平/垂直／データ影響）  
- **発見一覧**：`タイトル / リスク / 影響 / 再現手順 / PoC / 証跡 / WSTGテストID / 参考`  
- **攻撃連鎖図**（任意）：**入口→拡張→影響**を可視化（必要に応じて ATT&CK を併用）  
- **是正支援**：**再試験の条件**と**簡易セルフチェック**（WSTG該当節へのリンク）

---

## 6. スコープ外/安全配慮
- **DoS**・**大量送信**・**本番データ破壊**は原則禁止（個別合意がある場合のみ最小で実施）  
- 第三者資産・外部SaaS は**事前同意**または**スタブ**で代替  
- **ステージング優先**・**本番は最小限**・**証跡の機微情報マスキング**

---

## 7. 参考（一次資料／公式）
- **WSTG Latest（総目次）**  
  https://owasp.org/www-project-web-security-testing-guide/latest/  
- **The Web Security Testing Framework（SDLC全体の適用）**  
  https://owasp.org/www-project-web-security-testing-guide/latest/3-The_OWASP_Testing_Framework/0-The_Web_Security_Testing_Framework  
- **Penetration Testing Methodologies（PTES準拠）**  
  https://owasp.org/www-project-web-security-testing-guide/latest/3-The_OWASP_Testing_Framework/1-Penetration_Testing_Methodologies  
- **Web Application Security Testing（4章カテゴリ一覧）**  
  https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/  
- **Authentication（目次）**  
  https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/README  
- **Testing MFA**  
  https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/11-Testing_Multi-Factor_Authentication  
- **IDOR**  
  https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References  
- **Test HTTP Methods**  
  https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/06-Test_HTTP_Methods  
- **HTTP Parameter Pollution**  
  https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/04-Testing_for_HTTP_Parameter_Pollution  
- **SQL Injection**  
  https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/05-Testing_for_SQL_Injection  
- **Business Logic Data Validation**  
  https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/01-Test_Business_Logic_Data_Validation

---

### 付録：チェックリスト（最小）
- [ ] 前提合意（目的/範囲/禁止/アカウント/MFA/窓口/証跡）を締結  
- [ ] 4章カテゴリの**適用/除外**を明確化（理由付き）  
- [ ] 代表シナリオ **(S1)〜(S5)** を対象に合わせて採否決定  
- [ ] 重大発見は **再現手順+最小PoC** と **WSTGテストID** を必ず付記  
- [ ] 機密データの**痕跡削除/マスキング**を実施（期日まで）
