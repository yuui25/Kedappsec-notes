02_chains/path-basic-rce-system.md

# Path Traversal 基本→RCE（ファイルダウンロード/システム侵害）
**Tags:** family:path / variant:basic / engine:- / pivot:rce / impact:system  
**Refs:** ASVS（入力検証・ファイル取扱い）・WSTG（Directory Traversal / File Include）・MITRE ATT&CK（T1190 Exploit Public-Facing Application）・PortSwigger WSA（Path Traversal）・PayloadsAllTheThings（Directory Traversal）・HackTricks（File Inclusion/LFI→RCE）  
**Trace:** created: 2025-10-07 / last-review: 2025-10-07 / owner: you

## 概要
ファイルダウンロード（File download）機能のパス結合処理が不適切だと、`../` などで任意パスを参照できる（Path Traversal）。まずは任意ファイル読取（`/etc/hostname` 等）に到達し、次段でアプリのログ/セッション/テンプレート等を**ローカルファイル包含（LFI）**に食わせることで**リモートコード実行（RCE）**へ連鎖する。RCE はステージング限定で確認し、本番では**読取のみ**を非破壊で証明する。

## 前提・境界（Non-Destructive）
- 対象はステージング/検証環境。顧客合意のない本番環境でのコード実行は禁止。  
- PoC は「無害ファイル読取」と「ログへの**無害マーカー**注入」まで。コード断片は伏字（`****`）で示す。  
- サーバ負荷を与える大量列挙やワードリストは使用しない。  
- 実行権限昇格・横移動は記述上の可能性に留める。

## 入口（Initial Access）
- 典型パラメータ：`file`, `path`, `name`, `img`, `template`, `download`。  
- ペイロード最小例（Linux）：
```
GET /download?file=../../../../etc/hostname HTTP/1.1
Host: target.example
```
- フィルタ回避の基礎：二重URLエンコード（`..%252f`）、%2e表記、絶対パス、Windows系（`..\..\windows\win.ini`）、末尾ヌル廃止環境では使用不可。  
- エラー差分観察：存在/不在/権限エラー、レスポンス長、`Content-Type`。  
- 取得優先ファイル（読取のみ）：  
  - 実行に非影響：`/etc/hostname`, `/etc/issue`, アプリ設定の**公開情報寄り**ファイル  
  - 攻撃連鎖の足掛かり：アプリ設定・ログ保存先・テンプレート/キャッシュの実体パスを示す設定ファイル

## 横展開（Pivot / Privilege Escalation）
- **LFI経由RCEの典型連鎖**（PHP系の例。実行はステージングのみ）  
  1) パストラバーサルで**ログ/セッション/テンプレート**の保存パスを特定  
  2) アクセスログ等へ**無害マーカー**を書き込む（例：特異な`User-Agent`）  
  3) LFI可能なビュー機能（`/view?path=...`）で対象ログを読み込み→**挿入マーカー表示**を確認  
  4) ※本番では停止：ステージングでのみ、ログへ `<?php ****(); ?>` 等の**コード片**を注入 → ログを LFI で解釈させコマンド実行を確認  
- その他の連鎖オプション（解説のみ・実行禁止）：  
  - **php://filter** でソースを base64 読取 → 認可/キー流出から別経路でRCE  
  - **Nginx/Tomcatの正規化差**や**一時ファイル**を経由した LFI→RCE  
  - **ZIP Slip** 等の解凍系で書込面を得て Web ルートに配置（本カードは読取→RCE連鎖が主眼）

## 到達点（Impact）
- アプリ実行ユーザ権限での任意コマンド実行（`www-data` 等）  
- 秘密情報（資格情報・APIキー・ソース）流出、改ざん、横移動の足掛かり  
- サービス継続性への影響（停止・不正操作）  
- 監査/法的リスク（証跡改ざんのおそれ）

## 検知・証跡（Detection & Evidence）
- **Web/APサーバアクセスログ**：`../`、`%2e%2e%2f`、`/etc/passwd` 等の文字列、2xx/4xx 比率、レスポンスサイズ異常。  
- **アプリログ**：例外（`FileNotFound`, 正規化失敗）、パス検証メッセージ。  
- **相関**：`request-id`/`trace-id` と JST の時刻を突合。  
- **アラート例**：  
  - しきい値超過（単一IPからの `../` を含む 5 回/分以上）  
  - 高感度 IOC（`/proc/self/environ`, `php://filter` 文字列検出）  
- **証跡の最小確保**：リクエスト全文、レスポンスハッシュ（SHA256）、時刻（JST）、スクリーンショット（機微は伏字）。

## 是正・防御（Fix / Defense）
- **入力検証（Allowlist）** — *ASVS 5.0 V5.1*：  
  - ファイル名は**拡張子・文字種・長さ**で厳格許容。`..`, `/`, `\` を拒否。  
  - パスは**正規化（canonicalization）**後に**固定ベースディレクトリ**と**前方一致**で検証。  
- **サンドボックス/最小権限** — *ASVS 5.0 V5.2*：  
  - 読取専用サービスアカウント、`chroot`/コンテナ隔離、テンプレートはサンドボックス。  
- **アクセス制御** — *ASVS 5.0 V4*：  
  - 認可されたユーザのみ自身の領域に限定（IDベースでなく**サーバ側マッピング**）。  
- **安全なファイル取扱い** — *ASVS 5.0（File Handling）*：  
  - ユーザ指定パスは使用せず、**サーバ側ID→パス変換**（DB/マップ）を徹底。  
  - ログ/セッション/テンプレートは**Webルート外**に配置、実行拡張子の解釈無効化。  
- **ヘッダ/レスポンス強化**：`X-Content-Type-Options: nosniff`、ダウンロードは強制 `Content-Disposition: attachment`。  
- **監視**：WAF 署名（`../`, `php://`）, ルールチューニングと誤検知管理。  
- **開発プロセス**：ユニット/統合テストにパス正規化・拒否系のテストケースを追加。

## バリエーション
- OS/FS 差異：Linux（`/etc/passwd`）、Windows（`..\..\Windows\win.ini`）。  
- エンコード：二重/混合エンコード、Unicode 正規化、末尾スラッシュ/ドット、`;` 区切り（Tomcat など）。  
- ランタイム依存：`php://filter`、`expect://`、テンプレートエンジンのインクルード規則。  
- アプリ機能別：画像/CSVエクスポート/ログビューア/テンプレートプレビュー/バックアップ取得。  
- インフラ連鎖：リバースプロキシとアプリの**正規化不一致**、ZIP展開（Zip Slip）、シンボリックリンク。

## 再現手順（Minimal Steps）
1) 監視下の検証環境で `/download?file=../../../../etc/hostname` を送信し、**無害ファイル**が取得できるか確認。  
2) 取得結果・レスポンス長の差を記録（JST 時刻・`request-id` とともに）。  
3) `User-Agent: KEDA-PT-TEST-****` など**無害マーカー**を付与して任意リクエストを1回送信（ログ書込を想定）。  
4) LFI が疑われるビュー機能（例：`/view?path=../../../../var/log/httpd/access.log`）でマーカーの可視化を確認（**ここまで非破壊**）。  
5) ステージング合意時のみ、管理者同席の上で**安全なコマンド**（`id` 相当）をテンプレート/ログ包含で実行→ 実行ユーザ/作業証跡を取得。

## 参照
1. OWASP ASVS 5.0（公式）: https://owasp.org/www-project-application-security-verification-standard/  
2. OWASP WSTG: Testing Directory Traversal/File Include: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/01-Testing_Directory_Traversal_File_Include  
3. MITRE ATT&CK T1190 Exploit Public-Facing Application: https://attack.mitre.org/techniques/T1190/  
4. PortSwigger Web Security Academy — Path Traversal: https://portswigger.net/web-security/file-path-traversal  
5. PayloadsAllTheThings — Directory Traversal: https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Directory%20Traversal  
6. HackTricks — File Inclusion（LFI→RCEの連鎖含む）: https://book.hacktricks.xyz/pentesting-web/file-inclusion  
