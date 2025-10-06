# PortSwigger Web Security Academy で Webペネトレーションを始めるための要件  
*Kedappsec-notes / 01_reference 準拠*

---

## 目的と立ち位置（WSAの役割）
**Web Security Academy (WSA)** は PortSwigger が提供する無料のオンライン学習プラットフォームで、最新の研究・ドキュメントと**インタラクティブなラボ**により、手を動かして**エクスプロイト筋**を鍛えることを目的とします。  
- “学ぶ (Learn)” → “練習する (Practice)” → “試す (Test)” の循環で、**Burp Suite** の実運用スキルと攻撃の再現力を養います。  
- 学習パス（Learning Paths）により、初学者から実務者まで段階的に到達できます。

> 本書は **WSAを「Webペネトレーション（WebPT）」の開始要件に結び付けるための運用指針**です。スキャナ中心の**Web脆弱性診断**とは明確に切り分け、**攻撃連鎖の成立と影響実証**に焦点を当てます。

---

## Web脆弱性診断との明確な違い（根拠つき）
| 観点 | Web脆弱性診断（VA/DAST等） | Webペネトレーション（WebPT） |
|---|---|---|
| 目的 | 脆弱性の**存在**と**網羅**の把握 | **攻撃の成立**と**実害/到達範囲**の実証 |
| 手段 | 自動スキャン＋限定的手動確認 | 手動主体（Burp/手法）＋**攻撃連鎖**の試行 |
| 基準/定義 | OWASP: DAST（Vulnerability Scanning）は外形から脆弱性を探索する自動手法の総称 | OWASP WSTG/PTES: 事前合意のもとで**脆弱性を突いて侵攻**し、影響を評価 |
| 成果物 | 検出一覧・設定不備・既知パターン | 再現手順・PoC・証跡・**到達境界**（権限/データ/横展開） |
| 禁忌 | 破壊/業務影響の高い試験は禁止が標準 | **非破壊の原則**の範囲で、連鎖の成立を可能な限り実証 |

**一次資料（要点）**  
- OWASP *Web Security Testing Guide* は、Webアプリの**能動的分析**（弱点の発見と検証）を定義（WSTG）  
- OWASPは**スキャナ**を“Web Application Vulnerability Scanners（DAST）”として別枠で説明（VAとPTを役割分離）  
- WSTGは**ペネトレーションテスト手法**として PTES 等7フェーズ（Pre-engagement〜Reporting）を参照（PTの本質は**侵攻/実証**）  

---

## 開始要件（技術・運用）
### 1) アカウント & 環境
- **PortSwigger アカウント**（無料） … 進捗保存・ラボ利用に必須  
- **ブラウザ**（WSAラボはクラウド上に個別インスタンスを用意）  
- **Burp Suite**  
  - *Community*：手動ツール（Proxy/Repeater 等）中心で学習可能  
  - *Professional*：**Intruder** 高速化、**スキャナ**、**Collaborator の連携機能**など実務に近い体験  
- **Collaborator**（OAST）  
  - ラボでは PortSwigger 提供の**Collaborator サーバ**や**Exploit Server**が用意されることがある  
  - 実務では**プライベート Collaborator のデプロイ**により検出力を拡張（Blind SSRF/Blind XXE/Out-of-band XSS等）

### 2) 運用上の前提（非破壊・同意・証跡）
- **同意された範囲のみ**実施（対象・時間帯・送信レート・データ取り扱い）  
- **非破壊の原則**：DoS/大量トラフィック/破壊操作は書面許可がない限り禁止  
- **証跡**：HTTP リクエスト/レスポンス、スクリーンショット、ログ、時刻（TZ）を保存  
- **報告標準**：OWASP Risk Rating で影響評価、ASVS/ATT&CK ID で根拠付け

---

## 具体的な使い方（WSA → WebPT 実務へのブリッジ）
### A. 学習パスに沿う（最短ルート）
1. **Server-side vulnerabilities – Apprentice** から開始（基礎〜王道の攻撃面を体系化）  
2. トピック毎に「解説 → Labs」を往復し、**リクエスト改変の型**（Proxy→Repeater→Intruder）を体得  
3. **Collaborator/OAST**が関わるテーマ（SSRF/Blind Injection 等）は必ず**通知の見方**まで練習  
4. 各ラボ終了毎に、下記テンプレで**PT成果物**を1カード化（Gitに蓄積）

### B. ラボ→実務テンプレ（最小成果物・抜粋）
- **Finding 名**（例：IDORによる他者情報閲覧）／**到達境界**（データ種別/権限）  
- **前提/制約**（認証要否、役割、ツール/拡張機能、レート制御）  
- **再現手順（最小）**：`/account?id=123` → 124 で PII 露見 → **修正確認**  
- **PoC**：Burp Repeater スクリーンショット＋編集差分（必要なら cURL 併記）  
- **リスク評価**：OWASP Risk Rating（可能なら事業影響も添記）  
- **根拠タグ**：`ASVS v4.x: V*.*.* / WSTG-ID / ATT&CK T#### / WSA Topic`

### C. PortSwigger ならではの実務練習
- **Exploit Server の活用**：反射XSS/SSRF で外部ホストを要する PoC を安全に再現  
- **Mystery Lab**で“事前情報なし”の偵察〜仮説立案〜検証の反復練習  
- **Topic横断の連鎖**：**IDOR → CSRF → 帳票DL**、**SSTI → RCE → 内部探索** など**攻撃連鎖**をノート化

---

## よくある詰まりどころ（FAQ）
- **Community でも始められる？** → 可。まずは Proxy/Repeater を使い、**手動改変の精度**に集中。大量試行や自動化が必要になったら Pro を検討。  
- **Collaborator は必須？** → **Blind系**の検出・実証に不可欠。ラボでは提供サーバで練習、実務は**プライベート運用**も選択肢。  
- **ラボの成果を報告に落とすコツは？** → 「**再現手順が10分で追えるか**」を基準に、リクエスト前後の**差分**と**到達境界**を明記。

---

## 参考（一次情報・公式・安定）
- Web Security Academy（概要/無料/ラボ）  
  - https://portswigger.net/web-security  
  - 学習パス：https://portswigger.net/web-security/learning-paths  
  - トピック一覧：https://portswigger.net/web-security/all-topics  
  - Getting started（Burp動画）：https://portswigger.net/web-security/getting-started
- Burp Suite ドキュメント（機能・Collaborator/OAST）  
  - 総合ドキュメント：https://portswigger.net/burp/documentation  
  - Collaborator 概要：https://portswigger.net/burp/documentation/collaborator  
  - Collaborator（Desktop/手動手順）：https://portswigger.net/burp/documentation/desktop/tools/collaborator  
  - Typical uses（Blind検知例）：https://portswigger.net/burp/documentation/collaborator/uses  
  - Private Collaborator Server：https://portswigger.net/burp/documentation/collaborator/server/private
- OWASP（定義・区別の根拠）  
  - WSTG（セキュリティテストの定義/目的）：https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/00-Introduction_and_Objectives/README  
  - WSTG（Penetration Testing Methodologies/PTES）：https://owasp.org/www-project-web-security-testing-guide/latest/3-The_OWASP_Testing_Framework/1-Penetration_Testing_Methodologies  
  - Vulnerability Scanning（DAST/自動スキャナの説明）：https://owasp.org/www-community/Vulnerability_Scanning_Tools  
  - OWASP Risk Rating：https://owasp.org/www-community/OWASP_Risk_Rating_Methodology

---

## 付録：実施チェックリスト（抜粋・Kedappsec-notes用）
- [ ] 合意済みスコープ（対象/時間/禁止事項/影響閾値）を記録  
- [ ] 学習パスと対象トピックを事前選定（例：SSRF → Blind 系）  
- [ ] Burp プロジェクト作成／Proxy 設定／証跡保存ディレクトリ作成  
- [ ] Repeater/Intruder/Collaborator の**動作確認**（DNS解決/通知）  
- [ ] PoC 作成→**到達境界**の明文化→**修正検証**→レポート反映

