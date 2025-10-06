# PortSwigger Web Security AcademyではじめるWebペネトレーション：要件まとめ
*更新日: 2025-10-06*

このドキュメントは、**PortSwigger Web Security Academy（WSA）**を用いて**Webペネトレーション**（WebPT）を始めるための**実務的な要件**を整理したものです。単なる**Web脆弱性診断（Vulnerability Assessment）**との違いも明確化し、関連**ソース（公式ドキュメント・一次資料）**へのリンクを併記します。

---

## 1) 目的と立ち位置（診断との違い）
- **Web脆弱性診断**: 主に **自動・半自動の検出（スキャン）**で既知の弱点を洗い出し、広く漏れなく把握することを重視します。  
- **Webペネトレーション（WebPT）**: **攻撃者の視点で実際に脆弱性を悪用・連鎖**させ、**到達可能な被害（Impact）を実証**します。目標達成型で「どこまで侵害できるか」を示すのが本質です。  
  - 参考定義（NIST SP 800‑115 / 米NIST用語集）  
    - Penetration testing は**実攻撃を模擬し攻撃経路を実証**する行為と定義。  
    - 脆弱性診断はより広義の**テストとアセスメント**の一部であり、**発見**が重心。  
    - 出典: NIST SP 800‑115, NIST CSRC Glossary

**WSAの役割**: PortSwiggerが提供する**学習教材＋実践ラボ**を通じ、**実際に手を動かして exploit を成立**させる＝**WebPTの練習環境**です（無料）。

---

## 2) アカウントと学習パス
- **PortSwigger アカウント（無料）**  
  - 目的: **進捗の保存、ランク表示、学習パスの利用**。  
  - 出典: Getting started / All topics / All labs ページ（「Create an account」「Sign up」記載）
- **学習パスの選択（最初の一歩）**  
  - 初学者は **「Server‑side vulnerabilities（Apprentice）」** パスから開始するのがおすすめ。  
  - 出典: Learning paths / Server‑side vulnerabilities（Apprentice）

---

## 3) 必須ツールと環境
### 3.1 Burp Suite の版
- **Burp Suite Community Edition（無料）**で**大半のラボは学習可能**。  
- ただし、**OAST/Collaborator を要する一部ラボ**（例: **Blind SSRF** など）は**Professional**相当の機能が必要。  
  - 公式比較に**「Auto & manual OAST（Burp Collaborator）」は Professional の機能**と明記。  
  - さらに **SSRF（Blind, OAST）系ラボ**は**「デフォルトの Burp Collaborator 公開サーバを使う必要」**が明示。  
  - 出典: Burp CE/Pro 比較、SSRF Blind（OAST）系ラボの注意書き、Community EULA（Collaboratorは Pro/DASTに属する旨）

**実務的な指針**  
- まずは **Community** で始め、**Collaborator 必須の課題に到達したら Pro を検討**。  
- 代替として外部OAST（interact.sh等）を使う手法は一般サイトでは有用だが、**WSAラボは第三者宛て通信をブロック**しており**既定の Collaborator サーバ必須**のケースがある点に注意。

### 3.2 システム要件（パフォーマンス目安）
- **最小**: 2コア / 4GB RAM（基本のプロキシ・簡易Intruder）  
- **推奨**: 2コア / 16GB RAM（汎用）  
- **上級**: 4コア / 32GB RAM（大規模・複雑な攻撃やスキャン）  
- 出典: Burp System requirements

### 3.3 ブラウザ／プロキシ設定
- **Burp の組み込みブラウザ（Chromium）**推奨：**起動直後からMITM設定済み**でHTTPSもそのまま可。  
- 既存ブラウザを使う場合は**外部ブラウザのプロキシ設定**を実施。  
- 出典: Burp’s browser, External browser configuration

---

## 4) 進め方（最短ルート）
1. **Getting started** を読む → **Learning paths** で **Apprentice 系**から着手。  
2. **Burp 入門**（Proxy/Repeater/Intruder/Decoder/Comparer）を動画・ドキュメントで把握。  
3. **代表的な入門ラボ**（例: SQLi ログインバイパス、Path Traversal 基本形、認可回避 など）で、  
   - **Proxy**で**リクエスト改変** → **Repeater**で**再送** → 必要に応じて **Intruder** を使用、という**基本動線**を体で覚える。  
4. **中級以降**は **認可（Access control）／ビジネスロジック／キャッシュ中毒／リクエストスマグリング／LLM 攻撃**などに拡大。  
5. **OAST/Collaborator 必須ラボ**に進むタイミングで **Pro 導入**を検討。  
6. 到達目安として **BSCP（Burp Suite Certified Practitioner）**受験も選択肢。

出典: Getting started / All labs の各ラボ解説、BSCP ページ

---

## 5) 法的・安全面の前提
- **WSAのラボは意図的に脆弱で、第三者へ影響が出ないよう分離**。  
- **外部への任意通信はファイアウォールでブロック**される場合があり、**既定の Collaborator**のみ許容されるラボがある。  
- **Academyはバグバウンティ対象外**（報奨金の対象外）。  
- 出典: Blind SSRF（OAST）ラボの注意書き、PortSwigger Bug Bounty／FireBountyの注意書き

---

## 6) 最低限の基礎知識（推奨）
- **HTTP/HTTPS 基礎**（メソッド・ヘッダ・ボディ・クッキー・セッション、HTTP/2 の基本）  
  - 出典: Burp docs（HTTP/2 basics for Burp users）など
- **Burp 操作**（Proxy／Repeater／Intruder(CEは制限付)／Decoder／Comparer）  
  - 出典: Burp Getting started / Video tutorials

---

## 7) 典型的な「要件チェックリスト」
**開始前に以下を満たしていればOK**：
- [ ] PortSwigger **アカウント作成**（ダッシュボードで進捗を確認できる）  
- [ ] **Burp CE** をインストール（**必要になったら Pro 検討**）  
- [ ] **Burp の組み込みブラウザ**でラボにアクセスできる（もしくは外部ブラウザをプロキシ設定）  
- [ ] **学習パス（Apprentice）を1つ選択**（例: Server‑side vulnerabilities）  
- [ ] ラボ説明に従い、**Proxy → Repeater →（必要に応じ Intruder）** の流れで**Exploit を成立**させる  
- [ ] **OAST/Collaborator が必要なラボ**に備え、**Pro の試用／導入可否**を検討  
- [ ] （任意）**BSCP** を学習のマイルストーンに設定

---

## 8) 参考（ラボ例）
- **SQLi: ログインバイパス**（*Use Burp to intercept and modify the login request…*）  
- **パストラバーサル: 基本形**（`../../../etc/passwd` を使う基本例）  
- **認可: メソッドベース回避**（HTTPメソッドに依存する認可の欠陥を突く）  
- 出典: 各トピックの該当ラボ

---

## 9) 出典（公式・一次資料中心）
- **Academy 全般**  
  - Getting started: <https://portswigger.net/web-security/getting-started>  
  - All labs: <https://portswigger.net/web-security/all-labs>  
  - All topics（学習トピック一覧）: <https://portswigger.net/web-security/all-topics>  
  - Learning paths（学習パス）: <https://portswigger.net/web-security/learning-paths>  
  - Server‑side vulnerabilities（Apprentice）: <https://portswigger.net/web-security/learning-paths/server-side-vulnerabilities-apprentice>
- **Burp（導入・機能・要件）**  
  - Burp Getting started: <https://portswigger.net/burp/documentation/desktop/getting-started>  
  - System requirements: <https://portswigger.net/burp/documentation/desktop/getting-started/system-requirements>  
  - Burp’s browser（組み込みブラウザ）: <https://portswigger.net/burp/documentation/desktop/tools/burps-browser>  
  - 外部ブラウザ設定: <https://portswigger.net/burp/documentation/desktop/external-browser-config>  
  - CE/Pro 比較（OASTはProの機能と明記）: <https://portswigger.net/burp/communitydownload>
- **OAST / Collaborator 関連**  
  - Collaborator 概要: <https://portswigger.net/burp/documentation/desktop/tools/collaborator>  
  - Collaborator 設定: <https://portswigger.net/burp/documentation/desktop/settings/project/collaborator>  
  - Blind SSRF（OAST）ラボの注意書き（既定のCollaborator必須）: <https://portswigger.net/web-security/ssrf/blind/lab-out-of-band-detection>  
  - Burp Community EULA（Collaboratorは Pro/DASTに属する旨）: <https://portswigger.net/burp/eula/community>
- **ラボ（例）**  
  - SQLi（login bypass）: <https://portswigger.net/web-security/sql-injection/lab-login-bypass>  
  - Path Traversal（simple）: <https://portswigger.net/web-security/file-path-traversal/lab-simple>  
  - Access control（method-based bypass）: <https://portswigger.net/web-security/access-control/lab-method-based-access-control-can-be-circumvented>
- **法的・安全・運用**  
  - Blind SSRF（OAST）ラボの「第三者通信ブロック／既定Collaborator必須」注意書き: <https://portswigger.net/web-security/ssrf/blind/lab-out-of-band-detection>  
  - （参考）PortSwigger Bug Bountyでのアカデミー除外の言及: <https://firebounty.com/68-portswigger-web-security/>
- **定義・概念（診断 vs PT）**  
  - NIST SP 800‑115 本文（PDF）: <https://nvlpubs.nist.gov/nistpubs/legacy/sp/nistspecialpublication800-115.pdf>  
  - NIST CSRC 用語集（Penetration Testing）: <https://csrc.nist.gov/glossary/term/penetration_testing>

---

## 付録：最小セットのインストール＆起動（Burp CE）
1. PortSwigger公式から **Burp CE** をダウンロード＆インストール。  
2. Burp を起動し、**Proxy > Intercept > Open browser** で組み込みブラウザを起動。  
3. **Academy にログイン**し、**学習パスを開始**。  
4. ラボは**問題文 → 攻撃条件の把握 → リクエスト改変 → 成功判定（Solved）**の手順で進める。

---

**補足**: 本ドキュメントは、PortSwigger公式のガイダンス（WSA・Burp Docs）および NIST 一次資料を中心に要件化しています。運用ポリシーや端末要件は組織事情に合わせて調整してください。