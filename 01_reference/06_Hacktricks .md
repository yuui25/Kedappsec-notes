
# HackTricksに基づく Webペネトレーション開始要件

> 本資料は「HackTricks」を実務的な出発点として、**Webペネトレーションテスト（攻撃連鎖を含む実践的ペネトレ）** を始めるための要件をまとめたものです。  
> 「脆弱性診断（Vulnerability Assessment）」との違いを明確にし、必要な前提（スキル、環境、法務、ツール、学習リソース）と推奨ワークフローを提示します。

---

## 1. 定義の整理（重要）
- **Web脆弱性診断（Vulnerability Assessment）**：検出・分類・優先度付けが主目的。自動スキャン＋手動確認で脆弱性を列挙し報告する作業。  
- **Webペネトレーションテスト（Web Penetration Testing / WebPT）**：実際に攻撃手法を組み合わせて侵害可能性を証明し、攻撃連鎖（攻撃チェーン）を通じてより深いリスク／インパクトを評価する。証跡（PoC、ログ、スクリーンショット）やエクスプロイト開発が含まれる。  
  - 備考：本資料は後者（WebPT）にフォーカスする。citeturn0search3turn0search6

---

## 2. なぜHackTricksを出発点にするか（短評）
- HackTricksは実践的なテクニック集で、特定脆弱性への具体的な攻め方・回避法・ツール/ペイロードの例がまとまっているため、攻撃連鎖のアイデア出しやPoC作成に有用。citeturn0search9turn0search2

---

## 3. 必須（最低限）要件 — 技術スキル
1. **HTTP/HTTPSの深い理解**（メソッド、ヘッダ、ステータス、TLS） — 攻撃／検査の基盤。citeturn0search6  
2. **クライアント側技術の理解**（HTML, DOM, JavaScript, SPAの挙動） — XSS・ロジックフロー解析などで必要。citeturn0search12  
3. **認証・セッション管理の理解**（Cookie, JWT, OAuth フロー） — 認証バイパス検証の基礎。citeturn0search18  
4. **コマンドラインとスクリプト言語**（bash, Python/requests, curl） — PoC自動化や検証で必須。  
5. **Burp Suite 等のプロキシ／インターセプトツールの運用経験**（インターセプト、Intruder、Repeater、Macros） — 手動攻撃の中心。citeturn0search0

---

## 4. 必須（最低限）要件 — 環境・ツール
- **隔離された検査環境**（VPN/VDI/検査用ネットワーク）: 作業ログ・トラフィック管理と被害最小化のため。  
- **攻撃用ツール**：Burp Suite Professional（推奨）／Community、ffuf、sqlmap、nmap、wfuzz、httpie、socat、jq など。citeturn0search6  
- **学習＆参照サイト**：HackTricks（技術ノート）、PortSwigger Web Security Academy（実践ラボ）、PayloadsAllTheThings（ペイロード集）、OWASP WSTG/ASVS（チェックリスト）。citeturn0search9turn0search0turn0search8turn0search3

---

## 5. 必須（最低限）要件 — 法務／契約
- **スコープ明確化（ターゲットURL/範囲/認証情報/時間帯）**：範囲外の攻撃やDoSにならないよう明記。  
- **事前同意（POA/Authorization to Test）と責任分界**：再現性のある活動記録、連絡先（インシデント時のエスカレーション先）。  
- **非破壊ルールと緊急停止条件（例：データ改ざん禁止、業務停止閾値、バックアップ方針）**。  
  - 備考：WebPTは実際の攻撃を試すため、法務合意の厳密さが特に重要（診断よりハードな同意が必要）。citeturn0search6

---

## 6. 推奨プロセス（短いワークフロー）
1. **準備フェーズ**：スコープ合意、バックup確認、検査環境準備、認証情報の受領。  
2. **Recon（情報収集）**：ディレクトリ列挙、公開情報、APIドキュメント、フロントエンド資産（JS/Source）解析。HackTricksの各技術ページを参照して試行パターンを収集。citeturn0search9  
3. **脆弱性探索（探索→確認）**：自動スキャンで候補を出し、手動で深掘り（認証バイパス、IDOR、SSRF、XSS、SQLi、ファイルアップロード）を試す。PortSwiggerの学習パスで技術を補助。citeturn0search12turn0search0  
4. **攻撃連鎖（チェーン構築）**：単一脆弱性から権限昇格や情報漏洩へ繋ぐ再現手順・PoC作成。HackTricksのテクニックを組合せる。citeturn0search9  
5. **報告フェーズ**：要約（経営層向け）、技術詳細（再現手順・PoC・ログ・緩和案）、重大度・優先度。WSTG / ASVS のバージョンを参照して分類すると客観性が出る。citeturn0search3turn0search4

---

## 7. HackTricksを実務で使う際の留意点
- **情報の取捨選択**：HackTricksはテクニック集なので、環境にそのまま当てはまるとは限らない。検査対象の実装差分（フレームワーク/ミドルウェア）を確認してから適用する。citeturn0search9  
- **一次資料とのクロスチェック**：重要な攻撃手順や推奨緩和策はOWASP WSTG / ASVS、PortSwigger等の一次資料と突き合わせる習慣を持つ。citeturn0search3turn0search4  
- **ペイロード管理**：PayloadsAllTheThings等と併用して、エスケープやコーディング差分を考慮する。citeturn0search8

---

## 8. 推奨学習ロードマップ（短期）
1. PortSwigger Web Security Academy の「Getting started」→ 実践ラボ（Burp）をこなす。citeturn0search5  
2. HackTricksのトピック別（API、認証、ファイルアップロード、SSRFなど）を読み、該当する実験をラボで再現。citeturn0search9  
3. PayloadsAllTheThings を参照しながら自分用のペイロード集を作る。citeturn0search8  
4. OWASP WSTG/ASVS を参照して報告テンプレートやチェックリストを整備。citeturn0search3turn0search4

---

## 9. 主要参照（一次資料：読みやすい順）
1. HackTricks (GitBook / Repo) — 実践テクニック集。  
   - https://github.com/HackTricks-wiki/hacktricks  (GitHub) citeturn0search2turn0search9  
2. PortSwigger — Web Security Academy（ハンズオンラボ、Burpの使い方）。  
   - https://portswigger.net/web-security  citeturn0search0  
3. OWASP Web Security Testing Guide (WSTG) — テストメソドロジ & チェックリスト。  
   - https://owasp.org/www-project-web-security-testing-guide/  citeturn0search3  
4. OWASP ASVS — アプリケーションセキュリティの検証基準。  
   - https://owasp.org/www-project-application-security-verification-standard/  citeturn0search4  
5. PayloadsAllTheThings — ペイロード集。  
   - https://github.com/swisskyrepo/PayloadsAllTheThings  citeturn0search8

---

## 10. 最後に（チェックリスト）
- [ ] スコープと法的同意（POA）を取得済みか。  
- [ ] 検査用隔離環境を用意したか（ログ/キャプチャ確保）。  
- [ ] 必須ツール（Burp、ffuf、sqlmap 等）をセットアップしたか。  
- [ ] HackTricksの該当セクションを読み、関連ペイロードをローカルで検証したか。  
- [ ] 報告テンプレートにWSTG/ASVSの参照を入れているか。

---

### 著作・出典
本ドキュメントは上記一次資料（HackTricks、PortSwigger、OWASP、PayloadsAllTheThings）を参考に筆者（作成者）が要約・体系化したものです。各セクションの出典は本文中に記載しています。

