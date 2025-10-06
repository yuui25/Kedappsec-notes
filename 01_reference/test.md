# 99_web-pentest_requirements_complete.md
**総合版**：Webペネトレーション開始要件（ASVS × WSTG × ATT&CK × PortSwigger WSA × PayloadsAllTheThings × HackTricks）  
_本書は Kedappsec-notes/01_reference の README に準拠した統合ガイドです。_

> 位置づけ：ASVS=要件基準／WSTG=検証手順／ATT&CK=攻撃連鎖の説明／WSA=手を動かす訓練／PATT=入力ペイロード集／HackTricks=入口・バイパスの発想源。  
> 参考：`01_ASVS.md` / `02_WSTG.md` / `03_ATT&CK.md` / `04_PortSwigger_Web_Security_Academy.md` / `05_PayloadsAllTheThings.md` / `06_Hacktricks.md`

---

## 0. 用語と前提（VAとWebPTの違い）
**Web脆弱性診断（VA）**は既知弱点の**列挙・検証**が目的（自動スキャン比重大）。  
**Webペネトレーションテスト（WebPT）**は**実攻撃の成立**と**連鎖**・**影響**を**合意範囲内**で実証する行為。

- NIST SP 800-115（Vulnerability Scanning / Penetration Testing）  
  https://nvlpubs.nist.gov/nistpubs/legacy/sp/nistspecialpublication800-115.pdf
- OWASP Web Security Testing Guide（テスト手順の標準）  
  https://owasp.org/www-project-web-security-testing-guide/latest/
- ASVS（検証の**要件基準**、L1/L2/L3）  
  https://owasp.org/www-project-application-security-verification-standard/

> 本書は **非破壊の原則**（DoS/大量送信/破壊操作は事前書面合意がない限り実施不可）に従います。

---

## 1. 役割と使い分け（総覧）
| リファレンス | 立ち位置 | 使いどころ | 主要アウトプット | 注意点 |
|---|---|---|---|---|
| **ASVS** | セキュリティ**要件**の標準 | 深度（L1/L2/L3）と受入基準（DoD）を決める | 要件IDリスト・DoD・調達要件 | 手順ではない（WSTGと対で使う） |
| **WSTG** | テスト**手順/観点**の標準 | 計画〜実施〜報告の骨子 | 観点チェック・再現手順・証跡設計 | そのままでは過剰。対象に合わせて取捨選択 |
| **ATT&CK** | 攻撃者**行動様式**の辞典 | 侵入〜横展開〜影響の**連鎖説明** | 戦術/技術IDマッピング・データソース | PoC集ではない（“説明力”の強化） |
| **PortSwigger WSA** | ラボ&Burpドキュメント | 手を動かして**Exploit筋**を鍛える | ラボ成果→PoC化→報告粒度訓練 | 一部は Collaborator 等が必要 |
| **PayloadsAllTheThings (PATT)** | **ペイロード**&バイパス集 | 決めた観点に**何を入れるか** | Intruder向けリスト・バイパス | 破壊的/重負荷はステージング限定 |
| **HackTricks** | **入口・迂回**テク集 | 見落とし削減と発想出し | 403/401 bypass・HPP・HRS など | 一次資料（OWASP/公式）で裏取り前提 |

---

## 2. 使用順序・標準フロー（README準拠）
### Phase 0 — **Pre‑engagement（合意）**
- **目的/範囲/禁止事項/時間帯**、**アカウントとMFA**、**証跡方針**（Req/Res・スクショ・ログ保存）を合意（WSTG/PTES）。
- **ASVSレベル**（既定は L2、機微・規制強なら L3）と**DoD**を明記（ASVS v5 要件IDで）。

### Phase 1 — **Plan（設計）**
- **ASVS要件ID**をスコープに落とし込む（例：認証・セッション・アクセス制御・入力検証 等）。
- **WSTG 4章**から適用観点を選び、**除外理由**も明記。証跡テンプレを準備。

### Phase 2 — **Find Entry（入口を広げる）**
- **HackTricks**で入口候補（Host Header/CORS/HPP/Request Smuggling/403バイパス等）をブレスト。
- 候補ごとに **WSTG の該当節**を紐づけ、**PATT**で試験入力（payload）を即準備。

### Phase 3 — **Run（検証/実証）**
- **Burp**（Proxy→Repeater→Intruder）で**低リスク→高リスク**の順に実施。  
- **証跡**：各試行を**ASVS要件ID／WSTGテスト名**でタグ付け、Req/Resと時刻・相関IDを保存。

### Phase 4 — **Chain（連鎖を説明）**
- 成立箇所を**ATT&CK**戦術/技術IDでマッピング（Navigatorレイヤ推奨）。  
- 例：`TA0043 Recon → TA0001 T1190（Public-Facing App攻撃） → T1539（セッションCookie窃取） → T1078（有効アカウント再利用）`。

### Phase 5 — **Report & Retest（報告/再試験）**
- **エグゼクティブサマリ**＋**発見一覧**（リスク/影響/再現/PoC/証跡/参照ID）。
- **DoD判定**：採用レベルの**必須要件**が満たされたか。未達は**修正後再試験**。

---

## 3. 具体的な実施例（手順つき）
### 例A：**IDOR → 横展開（閲覧→更新）**
1. **入口当て**（HackTricks）：ID/参照値の直接操作、403/401 bypassの素振り。  
2. **観点/手順**（WSTG）：`4.5.4 Testing for Insecure Direct Object References` に沿って、閲覧→更新→削除の最小PoCを作成。  
3. **ペイロード**（PATT）：ID/配列/JSONパスの差し替え、HPP（`id=100&id=101`）も試す。  
4. **連鎖**（ATT&CK）：`TA0001 T1190`（公開アプリ悪用）として初期到達→`Privilege Escalation`相当の**垂直/水平昇格**を整理。  
5. **要件/判定**（ASVS）：アクセス制御要件IDの**未達**を記録、修正後に再テスト。

### 例B：**SSRF → メタデータ → 認証情報抽出**
1. **入口**（HackTricks）：URL受け取り機能（インポート/画像取得）を列挙。  
2. **観点**（WSTG）：SSRFのテスト節に従い内部到達の兆候（タイムアウト差/エラー）を観測。  
3. **ペイロード**（PATT）：`169.254.169.254` 等の段階的試行、スキーム/ヘッダ/リダイレクトの変種。  
4. **連鎖**（ATT&CK）：`T1190`→`Credential Access`系（取得トークンで**内部API**操作）。  
5. **報告**：**非破壊**の範囲で**最小実証**（例：限定キーの表示）に留め、**是正案**を添付。

### 例C：**HPP → 認可回避**
1. **入口**（HackTricks）：同名パラメータや配列化を試す。  
2. **観点**（WSTG）：`4.7.4 Testing for HTTP Parameter Pollution`。  
3. **ペイロード**（PATT）：順序/位置/符号化（URL/Unicode/二重）を変化させ比較。  
4. **連鎖**：ACL評価系の後段マージ仕様差を突き**管理UI露出**→**操作**へ。

> いずれも **証跡化**（Req/Res 原文＋差分／スクショ／時刻／関連ログ）と **参照ID（ASVS/WSTG/ATT&CK）**の付記を必須とする。

---

## 4. DoD（受入基準）とチェックリスト
**DoD例（貼って使う）**
```
準拠標準：OWASP ASVS v5.0.0
検証レベル：L2（合意によりL3/L1へ変更可）
スコープ：/auth, /admin, /api/*（ロール：user/admin）
判定：スコープに属するASVS必須要件IDがすべて「満たす」
未達：修正後に再検証（WebPTのPoC再実行を含む）
証跡：Req/Res・スクショ・ログ・Burpプロジェクト（相関ID/時刻）
```

**実施前チェック**
- [ ] 合意：目的/範囲/禁止/時間帯/窓口/MFA/証跡
- [ ] 深度：ASVSレベルと**要件IDリスト**（対象外は理由明記）
- [ ] 観点：WSTG 4章からの**適用観点表**と証跡テンプレ
- [ ] 安全：**非破壊**／負荷上限／レート制御／緊急停止手順

**実施後チェック**
- [ ] 重大発見は**再現手順＋最小PoC**＋**参照ID**を付記
- [ ] **ATT&CKマップ**（戦術/技術ID、データソース、検知可否）を更新
- [ ] **DoD判定**と**再試験方針**を明記

---

## 5. 成果物テンプレ
### 5.1 発見一覧（最小粒度）
| タイトル | リスク | 影響 | 再現手順（要約） | PoC/証跡 | 参照ID（ASVS/WSTG/ATT&CK） | 推奨対策 |
|---|---|---|---|---|---|---|

### 5.2 要件×証跡トレーサビリティ
| ASVS要件ID | 章/要約 | WSTGテスト | 結果 | 証跡リンク |
|---|---|---|---|---|

### 5.3 ATT&CK 連鎖シート
| Step | Tactic/Technique | 目的 | PoC/条件 | 期待ログ/実観測 | 検知可否 | 影響 |
|---|---|---|---|---|---|---|

---

## 6. 安全・法令遵守
- **書面許可のない試験は禁止**（PATT DISCLAIMERも参照）。
- **ステージング優先**／本番は**最小限**。可用性・データ保護を最優先。
- **データ衛生**：個人情報等は**マスキング**し、**削除期限**を設定。

---

## 7. 参考（一次資料リンク：安定版）
- **ASVS**（プロジェクトページ / v5）  
  https://owasp.org/www-project-application-security-verification-standard/  
  https://github.com/OWASP/ASVS
- **WSTG**（Latest / Testing Framework / 4章カテゴリ）  
  https://owasp.org/www-project-web-security-testing-guide/latest/  
  https://owasp.org/www-project-web-security-testing-guide/latest/3-The_OWASP_Testing_Framework/0-The_Web_Security_Testing_Framework  
  https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/
- **MITRE ATT&CK**（Top / Navigator / Data Sources）  
  https://attack.mitre.org/  
  https://mitre-attack.github.io/attack-navigator/  
  https://attack.mitre.org/datasources/DS0015/
- **PortSwigger Web Security Academy / Burp**（Learn / Labs / Intruder / Collaborator）  
  https://portswigger.net/web-security  
  https://portswigger.net/burp/documentation/desktop/tools/intruder  
  https://portswigger.net/burp/documentation/collaborator
- **PayloadsAllTheThings**（本体 / DISCLAIMER / 代表項目）  
  https://swisskyrepo.github.io/PayloadsAllTheThings/  
  https://swisskyrepo.github.io/PayloadsAllTheThings/DISCLAIMER/  
  HPP: https://swisskyrepo.github.io/PayloadsAllTheThings/HTTP%20Parameter%20Pollution/  
  SSRF: https://swisskyrepo.github.io/PayloadsAllTheThings/Server%20Side%20Request%20Forgery/
- **HackTricks**（Index / Web Methodology / 403/401 Bypass / HPP / HRS）  
  https://book.hacktricks.wiki/en/index.html  
  https://blog.1nf1n1ty.team/hacktricks/pentesting-web/web-vulnerabilities-methodology  
  https://angelica.gitbook.io/hacktricks/network-services-pentesting/pentesting-web/403-and-401-bypasses  
  https://angelica.gitbook.io/hacktricks/pentesting-web/parameter-pollution  
  https://portswigger.net/web-security/request-smuggling/exploiting

---

### 備考
- 本書は Kedappsec-notes **READMEの「推奨参照フロー」**に完全準拠しています。  
- 以降の更新では各一次資料の改訂に追随し、リンク健全性を定期点検してください。
