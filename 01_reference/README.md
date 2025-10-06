# 目次と参照指針

このフォルダは、Webペネトレーション（WebPT）を**一次資料中心**で実施・説明するための基準書庫です。  
「何を基準に」「どう使い分けるか」を明確にし、以降のチェーン設計・PoC・報告書作成の**根拠**を揃えます。

---

## 収録ドキュメント（本リポ内）

- **[01_ASVS.md](./01_ASVS.md)**：ASVSに基づくWebPT開始要件（要件基準／受入れ基準／DoD）  
- **[02_WSTG.md](./02_WSTG.md)**：WSTGに基づくWebPT開始要件（検証手順／前提合意／プロセス）  
- **[03_ATT&CK.md](./03_ATT&CK.md)**：MITRE ATT&CK準拠の攻撃連鎖マッピングと成果物要件  
- **[04_PortSwigger_Web_Security_Academy.md](./04_PortSwigger_Web_Security_Academy.md)**：WSAの活用（Burp／ラボ／学習パス）  
- **[05_Payloadallthethings .md](./05_PayloadsAllTheThings.md)**：PayloadsAllTheThingsの安全な活用と運用ルール  
- **[06_Hacktricks .md](./06_Hacktricks%20.md)**：HackTricksの実務適用（テクニック集の使いどころ）  
- **[99_web-pentest_requirements_complete.md](./99_web-pentest_requirements_complete.md)**：統合版（要件サマリと快速チェックリスト）

> **注意**：上記は**Kedappsec-notes専用**の運用文書です。「Webペネトレサービス化」等の別フォルダ運用方針ではありません。

---

## 役割と使い分け（要点サマリ）

| リファレンス | 立ち位置（何者か） | 使いどころ（いつ） | 典型アウトプット / 連携 | 注意点 |
|---|---|---|---|---|
| **ASVS** | セキュリティ**要件**の標準 | 受入れ基準・深度（L1/L2/L3）決定時 | Issueに**要件ID**を紐付け、DoDに反映 | 「手順」ではない。WSTG/報告と対で使う |
| **WSTG** | テスト**手順/観点**の標準 | スコープ確定後の検証設計 | 観点チェックリスト／再現手順の骨子 | そのまま実行せず、環境に合わせて取捨選択 |
| **ATT&CK** | 攻撃者**行動様式**の辞典 | 侵入〜横展開〜影響の**連鎖整理** | 攻撃経路図に**戦術/技術ID**を付す | 目的は“マッピング”。直接のPoC集ではない |
| **PortSwigger WSA** | ハンズオン**ラボ**＋Burpドキュメント | 手を動かして**Exploit筋力**を付ける | ラボ再現→リクエスト改変→成功判定の反復 | 一部ラボは**Collaborator(Pro)**必須 |
| **PayloadsAllTheThings** | 実践**ペイロード集** | 手法仮説が立った後の**試験入力** | Intruder用ワードリスト／バイパス例 | **ステージングで検証**→本番は合意内のみ |
| **HackTricks** | 実務**テクニック集** | 入口探索や**迂回手段**の発想出し | 認可回避、ヘッダ悪用、周辺Tips | 記述は広範。**一次資料で裏取り**前提 |

---

## 推奨参照フロー（最短ルート）

1. **ASVSで深度を決める**（L1/L2/L3）→合意文書に**DoD**として明記  
2. **WSTGで検証計画**を作る（前提合意／禁止事項／PoC方針）  
3. **HackTricks**で入口の当たりを広げ、**PayloadsAllTheThings**で具体ペイロードを用意  
4. 必要に応じて**WSAラボ**で手元再現→Burp操作の型を整える  
5. 攻撃成立後は**ATT&CK**で連鎖を整理し、報告書に**技術ID**を添付

---

## 品質ルール（このフォルダの運用）

- **根拠の優先度**：OWASP（ASVS/WSTG） ＞ MITRE（ATT&CK） ＞ NIST/PTES/PortSwigger ＞ HackTricks ＞ PayloadsAllTheThings  
- **表記**：ASVS要件は `vX.Y.Z-A.B.C`、ATT&CKは `T####(.###)` で明記  
- **非破壊の原則**：DoS/大量送信/破壊系は**書面許可なき限り禁止**  
- **PoC方針**：**ステージングで検証**→本番は**最小限・合意内**。必ず証跡（Req/Res・ログ・スクショ）を残す  
- **更新**：各ファイル末尾の「参考」を最新安定版に追随（リンク切れ点検）

---

## こんな時どれを見る？（クイックガイド）

- **深度や合格基準を決めたい** → `01_ASVS.md`  
- **検証の段取りと前提合意を整えたい** → `02_WSTG.md`  
- **侵入後の“どこまで行けたか”を説明したい** → `03_ATT&CK.md`  
- **Burpの型と手の感覚を養いたい** → `04_PortSwigger_Web_Security_Academy.md`  
- **試すべき入力やバイパス案が欲しい** → `05_Payloadallthethings .md`  
- **入口や迂回のアイデアを広げたい** → `06_Hacktricks .md`  
- **全体要件を一枚で見たい** → `99_web-pentest_requirements_complete.md`

---

## 参考（一次情報・公式）

- OWASP ASVS: https://owasp.org/www-project-application-security-verification-standard/  
- OWASP WSTG: https://owasp.org/www-project-web-security-testing-guide/latest/  
- MITRE ATT&CK: https://attack.mitre.org/  
- PortSwigger Web Security Academy: https://portswigger.net/web-security  
- PayloadsAllTheThings: https://swisskyrepo.github.io/PayloadsAllTheThings/  
- HackTricks: https://book.hacktricks.xyz/  
