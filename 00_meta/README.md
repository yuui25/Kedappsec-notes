# 00_meta 運用ガイド

このフォルダは **Kedappsec-notes の運用方針・命名規約・安全上の注意**を一元管理するためのメタドキュメント置き場です。

---

## リポジトリ基本方針
- **ブラウザ運用のみ**：ローカルスクリプトやCI（`.github`）は使用しない。
- **ステップ・バイ・ステップ**：1回の指示につき**1ファイル**を生成・更新する。
- **旧リポジトリは参照禁止**：明示の許可がある場合のみ参照可。
- **純Markdown**：フロントマターや埋め込みメタは付けない（GitHubにコピペで即貼り可）。
- **一次情報に基づく**：WSTG / ASVS / MITRE ATT&CK / PortSwigger / PayloadsAllTheThings / HackTricks などを優先。
- **教育・検証目的**：権限あるステージング環境で、**非破壊**を原則とする。

## 生成ルール（厳守）
1. **出力形式**：最初の行に**ファイルパス**、空行、続けて**Markdown本文**。例：  
   `00_meta/README.md` → 空行 → Markdown本文
2. **言語**：日本語。必要に応じて技術語を原語（英語）で括弧併記。
3. **ファイル命名**：`<フォルダ>/<YYYYMMDD>-<short-name>-v1.md`（テンプレや固定ファイルは除く）。
4. **参照の付け方**：本文末尾に**「参考」**節を作り、**一次情報URLのみ**を列挙。
5. **PoCポリシー**：
   - **非破壊**・**ステージング限定**。実運用破壊につながるコマンド・payloadの提示禁止。
   - 形態は**最小HTTP例（.http）/ 疑似コード / リクエスト-レスポンス例**に限定。
6. **禁止事項**：
   - 実行可能バイナリや秘密情報、実鍵の掲載。
   - 無差別・破壊的payload、実運用DB破壊等を誘発する具体コマンド。
   - `.github/` フォルダ、CI定義、シェルスクリプトの生成。
7. **修正の扱い**：ユーザからの「**修正：…**」は**差分ではなく全置換**（更新後の全文を再掲）。
8. **整合性の維持**：`05_prompts/maintain/` 内プロンプトを用いて、マトリクス/参照の整合性チェックを定期実行。
9. **ダウンロード提供**：必要に応じて**sandboxリンク**でMDを添付する（GitHubへそのまま貼付可能）。

## 作業フロー
- 新規作成：`次：<path>` の指示 → 指定ファイルのみ生成。
- 追記/修正：`修正：…` で差分要望 → **全文**を更新・再掲。
- 解説要求：`解説して` と明示がある場合のみ補足説明。
- 外部参照許可：`（参照して良い）` の明示がある場合に限り旧資料や外部断片の再利用可。
- ダウンロード：必要時にアシスタントが **sandbox:/…** 形式のリンクを提示。

## コミットメッセージ（推奨）
- 簡潔・要点先行。例：
  - `docs(meta): add repository operating rules`
  - `docs(matrices): seed attack_by_function entries`
  - `docs(poc): add safe HTTP example for idor`

## 安全・法令遵守（重要）
- 権限なき環境でのテスト、実データへのアクセス、業務影響が想定される操作は禁止。
- 必要に応じて**事前合意・チェンジウィンドウ・ロールバック計画・ログ/証跡取得**を準備。
- 重大リスクのPoCは**論述優先**（擬似コード/設計）で示し、実試験は別途ガードを敷く。

## 参考
- OWASP WSTG: https://owasp.org/www-project-web-security-testing-guide/latest/
- OWASP ASVS: https://owasp.org/www-project-application-security-verification-standard/
- MITRE ATT&CK: https://attack.mitre.org/
- PortSwigger Web Security Academy: https://portswigger.net/web-security
- PayloadsAllTheThings: https://github.com/swisskyrepo/PayloadsAllTheThings
- HackTricks: https://book.hacktricks.xyz/
