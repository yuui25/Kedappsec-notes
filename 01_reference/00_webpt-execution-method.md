01_reference/20251007-webpt-execution-method-v1.md

# Webペネトレーションテスト実施方法
> **対象**：Webペネトレーションテスト（WebPT）専用の進め方。**Web脆弱性診断（VA）とは明確に区別**し、一次情報（ASVS/WSTG/NIST/PTES/ATT&CK/WSA/PATT）へ**項目ごと**に参照URL（フルパス）を付す。

---

## 0. VA と WebPT の線引き（冒頭で依頼者と合意）
- **VA**：既知の弱点の**列挙/網羅**（主に自動＋限定手動）。
- **WebPT**：**実攻撃を模倣し、連鎖と影響を実証**（人手中心）。計画・合意・安全制御が必須。
- **合意書に最低限入れるもの**：範囲/除外、DoS等の禁止、監視・緊急連絡、証跡、成功基準（DoD）、再検証条件。

**参照URL（一次情報）**  
- NIST SP800-115（計画/試験/証跡）: https://csrc.nist.gov/pubs/sp/800/115/final  
- PTES（7フェーズ、Pre-engagement）: https://pentest-standard.readthedocs.io/en/latest/preengagement_interactions.html  

---

## 1. 契約締結直後：Pre-engagement（ヒアリング/合意書の具体）
**目的**：**何をどこまで**実施して良いかを**書面で固定**。ヒアリングシートを往復し、DoD・禁止事項・安全装置・ログ連携を確定。

**ヒアリングシート（最小セット）**
```
[対象] ドメイン/URL/API/テナント、環境（本番/ステージング）
[時間] 実施期間/時間帯、ピーク/バッチの考慮、メンテ窓
[禁止] DoS/大量送信/破壊操作/データ流出は禁止（例外は書面）
[アカウント] 役割別（一般/特権/監査）、MFA取扱い、初期化手順
[データ] 機密区分、疑似/匿名データ、持ち出し/消去ルール
[可用性] レート上限、監視・アラート調整、緊急停止手順/連絡先
[証跡] Req/Res原文・スクショ・Burpプロジェクト・サーバ/アプリログ・TZ
[成功基準(DoD)] ASVSレベル/適用要件、WSTGカバレッジ、再検証条件
```

**参照URL（一次情報）**  
- PTES Pre-engagement: https://pentest-standard.readthedocs.io/en/latest/preengagement_interactions.html  
- NIST SP800-115（計画・合意の重視）: https://nvlpubs.nist.gov/nistpubs/legacy/sp/nistspecialpublication800-115.pdf  

---

## 2. 設計（DoD／手順マッピング／連鎖の可視化）
### 2.1 DoD＝**ASVSで“深さ”を明示**
- 例：**ASVS v5 L2**を採用し、対象に関係する要件ID群で達成/未達を判定（契約書/計画書に明記）。

**参照URL**  
- OWASP ASVS プロジェクトTOP: https://owasp.org/www-project-application-security-verification-standard/  
- ASVS v5（最新情報）: https://owasp.org/www-project-application-security-verification-standard/migrated_content  

### 2.2 手順＝**WSTGの章節にマッピング**
- 情報収集〜APIまで、**どのWSTG節を適用/除外するか**を列挙して合意。

**参照URL**  
- WSTG TOP: https://owasp.org/www-project-web-security-testing-guide/  
- WSTG 最新版インデックス: https://owasp.org/www-project-web-security-testing-guide/latest/  

### 2.3 連鎖＝**ATT&CK IDで“攻撃の説明力”を付与**
- 例：**T1190（公開アプリ悪用）→ T1539（セッションCookie窃取）** と段階記録。報告書に戦術/技術IDを付与。

**参照URL**  
- ATT&CK T1190: https://attack.mitre.org/techniques/T1190/  
- ATT&CK T1539: https://attack.mitre.org/techniques/T1539/  
- ATT&CK データソース DS0015（Application Log）: https://attack.mitre.org/datasources/DS0015/  

---

## 3. 実施フロー（現場での“手”の動き）
### 3.1 Recon & 資産把握（安全なスタート）
- 公開面の列挙・指紋、隠しエンドポイント探索、ロボット/マニフェスト、クライアントアセット解析。
- 監視は**アプリログ（DS0015）**中心に事前合意。

**参照URL**  
- WSTG（全体導入/手法）: https://owasp.org/www-project-web-security-testing-guide/latest/  
- ATT&CK DS0015: https://attack.mitre.org/datasources/DS0015/  

### 3.2 代表的シナリオ（**WebPTは“連鎖”を作る**）
1) **認証/セッション**：弱いロックアウト→MFAフロー確認→セッション固定/使い回し→**Cookie奪取（T1539）**到達で連鎖記録。  
   **参照**：WSTG 認証群（4.3系/4.5系 など最新構成を適用）、ATT&CK T1539。  
   - T1539: https://attack.mitre.org/techniques/T1539/

2) **認可/IDOR**：ID直書き→**閲覧→更新→削除**の順に影響拡大を最小PoCで確認。  
   **参照**：WSTG Authorization Testing（ID一覧）。  
   - WSTG Authorization（README）: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/README  

3) **入力検証→横展開**：  
   - **HPP（同名パラメータ）**でマージ仕様差を突く→**ACL回避**へ派生。  
   - **SQLi**は読み取り→更新→認証回避→二次攻撃の順に段階化。  
   **参照**：  
   - WSTG HPP: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/04-Testing_for_HTTP_Parameter_Pollution  

4) **配置/運用の抜け**：**HTTP Methods** の誤許可（`OPTIONS/PUT/DELETE`等）→横展開。  
   **参照**：  
   - WSTG Test HTTP Methods: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/06-Test_HTTP_Methods  

5) **API/SSRF**：外部URL入力点から**メタデータ/内部面**到達の可否を段階化（Blind系は**Collaborator**）。  
   **参照**：  
   - PortSwigger Collaborator 概要: https://portswigger.net/burp/documentation/collaborator  
   - Collaborator 入門: https://portswigger.net/burp/documentation/desktop/tools/collaborator/getting-started  
   - Web Security Academy（API/SSRF系ラボ）: https://portswigger.net/web-security/all-labs  

### 3.3 手技（ツールとペイロードの使い分け）
- **WSA**でBurpの**Proxy/Repeater/Intruder/Collaborator**を実戦練習→実務適用。  
- **PayloadsAllTheThings（PATT）**で入力値を素早く準備（**低リスク→高リスク**で段階化）。

**参照URL**  
- Web Security Academy TOP: https://portswigger.net/web-security  
- PayloadsAllTheThings（GitHub）: https://github.com/swisskyrepo/PayloadsAllTheThings  
- PayloadsAllTheThings（Web版）: https://swisskyrepo.github.io/PayloadsAllTheThings/  

---

## 4. 証跡（Evidence）設計：**先に決める**
- 収集物：HTTP **Req/Res原文**・スクショ・Burpプロジェクト・**アプリログ（DS0015）**・相関ID・TZ。
- 紐付け：各試行へ **ASVS要件ID / WSTG節 / ATT&CK技術ID** をタグ付け。
- 再現性：最小手順＋差分（10分で再現できる粒度）。

**参照URL**  
- NIST SP800-115（試験ログ/影響管理/再現性）: https://csrc.nist.gov/pubs/sp/800/115/final  
- ATT&CK DS0015: https://attack.mitre.org/datasources/DS0015/  

---

## 5. 安全配慮（Non-destructive 原則）
- **DoS/大量送信/破壊操作は禁止**（例外は書面・時間帯/回数制御で）。  
- 本番は**最小限**、**ステージング優先**。データ汚染/キュー滞留/キャッシュ汚染の**ロールバック手順**を準備。

**参照URL**  
- PTES（事前取決め）: https://pentest-standard.readthedocs.io/en/latest/preengagement_interactions.html  
- NIST SP800-115（リスク管理）: https://nvlpubs.nist.gov/nistpubs/legacy/sp/nistspecialpublication800-115.pdf  

---

## 6. 報告（Deliverables）
**1) エグゼクティブサマリ**：到達点（未認証→認証後／水平→垂直／データ影響）。  
**2) 連鎖図**：**ATT&CK ID**で各ステップ表示（例：`TA0001/T1190 → TA0006/T1539`）。  
**3) 個別Finding（テンプレ）**
```
[タイトル] 例：IDORで他ユーザの注文詳細に閲覧到達
[影響] PII/注文データの閲覧/更新/削除（件数/範囲）
[前提] 役割/MFA/初期状態
[再現] 手順番号＋Req/Res差分（最小PoC）
[PoC] Burp Repeater差分 or cURL（非破壊）
[検知] アプリログ（DS0015）の観測/検知可否
[根拠] ASVS要件ID, WSTG節, ATT&CK T***
[推奨] 暫定策（即日）＋恒久策（設計/実装/運用）
```
**4) DoD判定表**：ASVSレベルの必須要件に対する達成/未達と再検証条件。

**参照URL**  
- ATT&CK T1190: https://attack.mitre.org/techniques/T1190/
- ATT&CK T1539: https://attack.mitre.org/techniques/T1539/
- ASVS TOP: https://owasp.org/www-project-application-security-verification-standard/
- WSTG 最新: https://owasp.org/www-project-web-security-testing-guide/latest/

---

## 7. 再検証（Fix Verify）とクローズ
- **修正差分の再現**＋**副作用（ログ/監視/アラート）**確認。  
- **ASVS DoD**の未達解消を明示。次回（年次/リリース毎）の継続検証へ接続。

**参照URL**  
- ASVS v5: https://owasp.org/www-project-application-security-verification-standard/migrated_content  

---

## 付録A：サンプルPoC（安全素振り用・ステージング限定）
> **注意**：非破壊かつ最小の差分確認に限定。業務データには触れない。

```http
# HPP（同名パラメータのマージ差分）
GET /admin?role=user&role=admin HTTP/1.1
Host: target.example

# 認可テスト（ID直書き・閲覧のみ）
GET /api/orders/124 HTTP/1.1
Cookie: session=...

# HTTP Methods（誤許可の確認：Read Only）
OPTIONS /api/admin HTTP/1.1
```

**参照URL**  
- WSTG HPP: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/04-Testing_for_HTTP_Parameter_Pollution  
- WSTG Test HTTP Methods: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/06-Test_HTTP_Methods  

---

## 付録B：学習と手技の底上げ（実践ラボ）
- **WSA Learning Paths / All Labs** を進め、手順化と再現力を習慣化。

**参照URL**  
- Web Security Academy（All Labs）: https://portswigger.net/web-security/all-labs  
- Web Security Academy（Learning Paths）: https://portswigger.net/web-security/learning-paths  

---

## 参考（一次情報・総覧）
- OWASP ASVS（TOP）: https://owasp.org/www-project-application-security-verification-standard/  
- OWASP ASVS v5（最新情報）: https://owasp.org/www-project-application-security-verification-standard/migrated_content  
- OWASP WSTG（TOP）: https://owasp.org/www-project-web-security-testing-guide/  
- OWASP WSTG（最新版インデックス）: https://owasp.org/www-project-web-security-testing-guide/latest/  
- NIST SP800-115（csrc）: https://csrc.nist.gov/pubs/sp/800/115/final ／（PDF直） https://nvlpubs.nist.gov/nistpubs/legacy/sp/nistspecialpublication800-115.pdf  
- PTES Pre-engagement: https://pentest-standard.readthedocs.io/en/latest/preengagement_interactions.html  
- MITRE ATT&CK（T1190/T1539/DS0015）: https://attack.mitre.org/techniques/T1190/ ／ https://attack.mitre.org/techniques/T1539/ ／ https://attack.mitre.org/datasources/DS0015/  
- PortSwigger Web Security Academy（TOP/All Labs/Collaborator）: https://portswigger.net/web-security ／ https://portswigger.net/web-security/all-labs ／ https://portswigger.net/burp/documentation/collaborator  
- PayloadsAllTheThings（GitHub/Web）: https://github.com/swisskyrepo/PayloadsAllTheThings ／ https://swisskyrepo.github.io/PayloadsAllTheThings/
