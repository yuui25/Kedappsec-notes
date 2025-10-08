# 目次と参照指針

このフォルダは、Webペネトレーション（WebPT）を**一次資料中心**で実施・説明するための基準書庫です。  
「何を基準に」「どう使い分けるか」を明確にし、以降のチェーン設計・PoC・報告書作成の**根拠**を揃えます。  
※ 本フォルダは **Kedappsec-notes 専用**の運用文書です。

---

## 収録ドキュメント（本リポ内）

- **[00_webpt-integrated-flow.md](./00_webpt-integrated-flow.md)**：WebPT実施フローの**統合版**（順序・運用の“型”）  
- **[01_ASVS.md](./01_ASVS.md)**：ASVSに基づく要件基準（深度/DoD/受入れ基準）  
- **[02_WSTG.md](./02_WSTG.md)**：WSTGに基づく検証観点・設計（Testing Framework）  
- **[03_ATT&CK.md](./03_ATT&CK.md)**：MITRE ATT&CKでの攻撃連鎖マッピング（戦術/技術/データソース）  
- **[04_PortSwigger_Web_Security_Academy.md](./04_PortSwigger_Web_Security_Academy.md)**：WSA活用（Burpの型／ラボ→PoC）  
- **[05_PayloadsAllTheThings.md](./05_PayloadsAllTheThings.md)**：PayloadsAllTheThingsの安全な活用（入力・バイパス）  
- **[06_Hacktricks .md](./06_Hacktricks%20.md)**：HackTricksの実務適用（入口拡張／迂回手段の発想）

> 旧文書（例：`00_webpt-execution-method.md`、`99_web-pentest_requirements_complete.md`）はアーカイブ済み。

---

## 役割と使い分け（要点サマリ）

| リファレンス | 立ち位置（何者か） | 使いどころ（いつ） | 典型アウトプット / 連携 | 注意点 |
|---|---|---|---|---|
| **Integrated Flow** | 実施**順序の定型** | 着手前〜実施中の全フェーズで参照 | 進め方・証跡・報告の型 | 個別詳細は各一次資料で裏取り |
| **ASVS** | セキュリティ**要件**の標準 | 深度（L1/L2/L3）と**DoD**決定時 | Issue/DoDに**要件ID**を付す | 「手順」ではない。WSTGと対で使用 |
| **WSTG** | テスト**観点/手順**の標準 | スコープ確定後の**検証設計** | 観点表／再現手順骨子 | 環境に合わせて取捨選択 |
| **ATT&CK** | 攻撃者**行動様式**の辞典 | 侵入〜横展開〜影響の**連鎖整理** | 経路図へ**戦術/技術ID** | 目的は“マッピング”。PoC集ではない |
| **WSA** | ハンズオン**ラボ**＋Burp Docs | 手技の**再現訓練**と型作り | Repeater/Intruder差分 | 一部機能はPro前提（Collaboratorなど） |
| **PATT** | 実践**ペイロード集** | 手法仮説後の**試験入力** | Wordlist/バイパス例 | **ステージング優先**、本番は合意内 |
| **HackTricks** | 実務**テクニック集** | 入口探索や**迂回手段**の発想出し | 認可回避/ヘッダ悪用/Tips | 記述広範。**一次資料で裏取り**必須 |

---

## 推奨参照フロー（最短ルート）

1. **Integrated Flow**（`00_webpt-integrated-flow.md`）を常時参照して全体の道筋を確認  
2. **ASVSで深度/DoDを決める** → 合意文書へ要件IDで明記  
3. **WSTGで検証計画**（前提合意／禁止事項／PoC方針）を作る  
4. **HackTricks**で入口の当たりを広げ、**PATT**で具体ペイロードを用意  
5. 必要に応じて**WSAラボ**で手元再現→Burp操作の型を整える  
6. 成立箇所を**ATT&CK**で連鎖化し、報告書に**技術ID**と**データソース**を添付

---

## 品質ルール（このフォルダの運用）

- **根拠の優先度**：OWASP（ASVS/WSTG） ＞ MITRE（ATT&CK） ＞ NIST/PTES/PortSwigger ＞ HackTricks ＞ PayloadsAllTheThings  
- **表記ルール**：ASVS要件は `vX.Y.Z-A.B.C`、ATT&CKは `T####(.###)` を厳守  
- **非破壊の原則**：DoS/大量送信/破壊系は**書面許可なき限り禁止**  
- **PoC方針**：**ステージング検証**を基本／本番は**最小限・合意内**。証跡（Req/Res原文・ログ・スクショ）必須  
- **更新運用**：各ファイル末尾の「参考」を**最新安定版**へ追随（リンク切れ点検を定例化）

---

## こんな時どれを見る？（クイックガイド）

- **まず全体の型を掴みたい** → `00_webpt-integrated-flow.md`  
- **深度や合格基準（DoD）を決めたい** → `01_ASVS.md`  
- **検証の段取りと前提合意を整えたい** → `02_WSTG.md`  
- **“どこまで行けたか”を説明したい** → `03_ATT&CK.md`  
- **Burpの型と手の感覚を養いたい** → `04_PortSwigger_Web_Security_Academy.md`  
- **試すべき入力やバイパス案が欲しい** → `05_PayloadsAllTheThings.md`  
- **入口や迂回のアイデアを広げたい** → `06_Hacktricks .md`

---

## 参考（一次情報・公式）

- OWASP ASVS: https://owasp.org/www-project-application-security-verification-standard/  
- OWASP WSTG: https://owasp.org/www-project-web-security-testing-guide/latest/  
- MITRE ATT&CK: https://attack.mitre.org/  
- PortSwigger Web Security Academy: https://portswigger.net/web-security  
- PayloadsAllTheThings: https://swisskyrepo.github.io/PayloadsAllTheThings/  
- HackTricks: https://angelica.gitbook.io/hacktricks
