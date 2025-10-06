# 03_ATT&CK — MITRE ATT&CKで始めるWebペネトレーション要件

> **本ドキュメントは *Kedappsec-notes/01_reference* 専用**です。Webペネトレ「サービス運用方針」ではありません（別管理）。

---

## 1. 目的と立ち位置（何者か）
MITRE ATT&CKは**攻撃者の行動様式（戦術・技術）**を体系化した知識ベース。Webペネトレーション（WebPT）では、**「どの行動をどこまで再現できたか」を戦術/技術IDで可視化**し、侵入経路・連鎖・検知/証跡を説明するために用いる。  
- 公式: https://attack.mitre.org/  
- Get Started: https://attack.mitre.org/resources/  
- ATT&CK Navigator: https://mitre-attack.github.io/attack-navigator/

**本ドキュメントの役割**：WSTG/ASVSと並走しながら、WebPTの**攻撃連鎖の根拠付け**・**成果物の粒度**を定義すること。

---

## 2. Web脆弱性診断（VA）との明確な区別
- **VA**：脆弱性の**列挙・検証**（スキャン＋手動検証）。報告の“単位”は*個別の弱点*。  
  - 参考（NIST SP 800-115）: https://csrc.nist.gov/pubs/sp/800/115/final
- **WebPT**：攻撃者の**行動連鎖**で*目的達成可能性*を示す。**初期侵入→横展開→影響**までを、ATT&CKの**戦術/技術ID**でマッピングし、検知可能性/証跡を評価。

> **単位の違い**：VA＝脆弱性、WebPT＝攻撃経路（戦術→技術の連鎖）。

---

## 3. 開始要件（Definition of Ready）
1) **前提合意**  
   - テスト環境の優先（本番は**非破壊・最小限**）。DoS/大量送信は禁止（書面許可がある場合を除く）。  
   - スコープ（ドメイン/機能/API/クラウド面：SaaS/IdP/IaaS）と**禁止事項**、観測・ロギング方針を確定。
2) **基準の明示**  
   - **戦術/技術ID**で記録（例: `TA0001 / T1190`）。  
   - ASVSで深度（L1/L2/L3）やDoDを決め、WSTGで検証観点を設計（相互参照は別紙）。
3) **証跡設計（先に決める）**  
   - どの**データソース**で何を残すか（例：Application Log / DS0015）。  
   - 依頼者側の監査ログ、WAF/CDN、IdP、APMの取得方法。

> 記録フォーマット：テクニックID／目的／PoC概要／成功条件／ログ項目／検知可否／リスク評価。

---

## 4. 具体的な使い方（実務手順）

### 4.1 マトリクスを選ぶ（対象に応じて）
- **Enterprise**（オンプレ/一般Web）：https://attack.mitre.org/techniques/enterprise/  
- **Cloud/SaaS**（IdP/Office/SaaS/IaaS）：https://attack.mitre.org/matrices/enterprise/cloud/ ・ https://attack.mitre.org/matrices/enterprise/cloud/saas/

### 4.2 Navigatorでレイヤーを作る
- Hosted版：https://mitre-attack.github.io/attack-navigator/  
- レイヤー項目（推奨）：`status`（試験済/未着手/不適用）、`impact`（High/Med/Low）、`evidence`（ログ/スクショ/Req-Resリンク）、`notes`（WSTG/ASVS対応）。
- 目的別にレイヤーを分ける：①**侵入経路**、②**横展開**、③**影響**、④**検知/ログ**。

### 4.3 Web特有の“まず当てる”テクニック（例）
- **Reconnaissance（TA0043）**  
  - **T1595 Active Scanning**（.001 IPブロック／.002 脆弱性スキャン／.003 **Wordlist Scanning**＝コンテンツ発見）  
    - https://attack.mitre.org/techniques/T1595/ ・ https://attack.mitre.org/techniques/T1595/002/ ・ https://attack.mitre.org/techniques/T1595/003/
- **Initial Access（TA0001）**  
  - **T1190 Exploit Public-Facing Application**（典型：RCE/SSRF/テンプレ注入）  
    - https://attack.mitre.org/techniques/T1190/
- **Credential Access / Defense Evasion**  
  - **T1539 Steal Web Session Cookie**（セッション奪取）https://attack.mitre.org/techniques/T1539/  
  - **T1078 Valid Accounts**（クラウド/ローカル/ドメイン）https://attack.mitre.org/techniques/T1078/
- **Persistence（必要に応じてシミュレート）**  
  - **T1505.003 Web Shell**（サーバに常駐Webシェル）https://attack.mitre.org/techniques/T1505/003/

### 4.4 ログと検知のマッピング
- 例：**DS0015 Application Log**（Exploit/例外/500系/権限エラー）→試験ごとに**期待ログ**と**実観測**を突合。  
  - https://attack.mitre.org/datasources/DS0015/

### 4.5 DoD（Definition of Done）
- ① Navigatorレイヤーに**試験結果**が反映  
- ② **攻撃経路図**（戦術→技術の連鎖）と**成功条件/影響**が文章化  
- ③ **証跡**（Req/Res・スクショ・ログ引用）が揃い、再現手順（WSTG粒度）が付属  
- ④ ASVS/WSTG/ATT&CKの**参照ID**が全て成果物に明記

---

## 5. サンプル攻撃連鎖（Webアプリ）

**目的**：一般ユーザ→他ユーザ情報の窃取（権限外閲覧）

1) **TA0043 Recon** → **T1595.003 Wordlist Scanning**：隠しエンドポイント探索  
2) **TA0001 Initial Access** → **T1190 Exploit Public-Facing App**：認可欠陥＋IDORで他ユーザデータ取得  
3) **TA0006 Credential Access** → **T1539 Steal Web Session Cookie**：XSS経由でCookie奪取（場合により）  
4) **TA0005 Defense Evasion** / **TA0003 Persistence** → **T1078 Valid Accounts**（奪取済みトークン/再利用）  
5) **TA0010 Exfiltration**：API経由で対象データ搬出（最小量・合意内で検証）

> 各手順に**PoC概要**・**成功条件**・**ログ期待/実測**・**影響**を記述。

---

## 6. 成果物テンプレ（貼り付けて使う）

| Step | Tactic/Technique | 目的 | PoC/再現手順（要約） | 成功条件 | 期待ログ/実観測 | 検知可否 | 影響 |
|---|---|---|---|---|---|---|---|
| 1 | TA0043 / T1595.003 | 隠し資産発見 | wordlistで`/admin/exports`探索 | 200/302取得 | access_logに`GET /admin/exports` | △ | 低 |
| 2 | TA0001 / T1190 | 初期侵入 | IDORで他ID指定→レスポンス確認 | 200で他者情報 | app_logに権限例外なし | × | 中 |
| 3 | TA0006 / T1539 | セッション奪取 | 反射XSSでCookie exfil | Cookie有効で再現 | WAF/JS監査ログ | △ | 高 |
| 4 | TA0005 / T1078 | 回避/持続 | 奪取認証を再利用 | 再ログイン成功 | IdP/アクセスログ | △ | 高 |

---

## 7. 参考（一次情報）
- ATT&CK（トップ/概要/ツール）  
  - Top: https://attack.mitre.org/  
  - Get Started: https://attack.mitre.org/resources/  
  - Navigator（Hosted）: https://mitre-attack.github.io/attack-navigator/ ・ Data & Tools: https://attack.mitre.org/resources/attack-data-and-tools/
- 主要テクニック  
  - TA0043 Reconnaissance: https://attack.mitre.org/tactics/TA0043/  
  - T1595 Active Scanning / .002 Vulnerability / .003 Wordlist:  
    - https://attack.mitre.org/techniques/T1595/ ・ https://attack.mitre.org/techniques/T1595/002/ ・ https://attack.mitre.org/techniques/T1595/003/  
  - TA0001 Initial Access: https://attack.mitre.org/tactics/TA0001/ ・ T1190 Exploit Public-Facing App: https://attack.mitre.org/techniques/T1190/  
  - T1539 Steal Web Session Cookie: https://attack.mitre.org/techniques/T1539/  
  - T1078 Valid Accounts（.001/.002/.003/.004）: https://attack.mitre.org/techniques/T1078/  
  - T1505.003 Web Shell: https://attack.mitre.org/techniques/T1505/003/  
  - データソース DS0015 Application Log: https://attack.mitre.org/datasources/DS0015/
- 定義・区別（VAとPT）  
  - NIST SP 800-115: https://csrc.nist.gov/pubs/sp/800/115/final

---

### 運用メモ
- 本書は**一次資料中心**・**非破壊原則**・**ID明記**の品質ルールに従う（ASVS/WSTG/ATT&CKの参照IDを成果物へ）。
