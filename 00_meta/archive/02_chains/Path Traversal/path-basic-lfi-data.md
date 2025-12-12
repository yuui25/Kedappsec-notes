02_chains/path-basic-lfi-data.md

# パストラバーサル/LFIでデータ閲覧（path/basic/lfi/data）
**Tags:** family:path / variant:basic / engine:- / pivot:lfi / impact:data  
**Refs:** ASVS（Validation & File Handling）・WSTG（Directory Traversal & File Include）・MITRE ATT&CK（Exploit Public-Facing Application）・PortSwigger WSA（File path traversal）・PayloadsAllTheThings（Directory Traversal）・HackTricks（File Inclusion）  
**Trace:** created: 2025-10-07 / last-review: 2025-10-07 / owner: you

## 概要
ファイルダウンロード機能（例：`GET /download?path=<file>`）で、ユーザ入力のパス結合と検証が不十分な場合、相対パス（`../`）や絶対パスを用いて**ローカルファイルインクルージョン（LFI）**が成立し、アプリ外の機微ファイル（設定、ソース、ログ）を**非破壊で閲覧**できる。到達点はデータ閲覧だが、後述の横展開により認証情報の抽出→さらなる侵害に繋がる。

## 前提・境界（Non-Destructive）
- 対象はステージング/検証環境。**本番での機微ファイル閲覧や大量ダンプは禁止**。  
- リクエストは**最小限**。確認用には無害なファイル（例：`/etc/hostname`、`C:\Windows\win.ini`）を用いる。  
- 認可境界（認証後/権限別）を尊重し、**非破壊・最低権限**で実施。  

## 入口（Initial Access）
- 典型的な入口  
  - ダウンロード/プレビュー/テンプレート読込/画像読み込み/エクスポート（CSV/PDF）機能の`path`,`file`,`template`,`include`等のパラメータ。  
  - 直感的ヒント：拡張子制限が緩い、またはパスの一部だけを前置（`/static/`）して結合している実装。  
- 最小PoC（相対パス）  
```
GET /download?path=../../../../etc/hostname HTTP/1.1
Host: target.example
```
- 代替PoC（URLエンコード/OS差異）  
```
GET /download?path=..%2f..%2f..%2f..%2fWindows%2Fwin.ini HTTP/1.1
Host: target.example
```

## 横展開（Pivot / Privilege Escalation）
- **構成/資格情報の発見**：`/etc/passwd`、アプリ設定（DB接続、APIキー）、`/var/log/app.log`、`.env`、`web.config`/`application.yml`等から秘密情報を抽出。  
- **ソースコード閲覧**：入力検証/署名鍵/内部エンドポイント（SSRF/管理API）を把握。  
- **セッション/トークン**：セッションファイル、トークン保存箇所の特定（※閲覧のみ、改変は禁止）。  
- **二次攻撃の足がかり**：ログポイズニングやアップロード機能と組み合わせたLFI→RCE連鎖の可能性評価（**本カードでは実行しない**）。  

## 到達点（Impact）
- アプリ外ディレクトリの**データ閲覧**（設定、ソース、ログ、ユーザ生成物）が可能。  
- 二次的影響：認証情報再利用、内部ネットワーク可視化、権限昇格の調査容易化。  

## 検知・証跡（Detection & Evidence）
- **Webサーバ/リバプロ**：`access.log`で`../`やエンコード（`%2e%2e%2f`、二重エンコード）を検知。例：`2025-10-07T10:21:34+09:00 req_id=abc123 user=tester GET /download?path=..%2f..%2f..%2fetc%2fhostname 200 123B`  
- **アプリログ**：例外（`FileNotFound`/`PermissionDenied`）、正規化（canonicalization）失敗メッセージ、検証警告。  
- **相関**：`X-Correlation-ID`/`trace_id`とユーザID/IP/UAのひも付け。  
- **アラート例**：  
  - しきい値超過（`../`や`%2e%2e%2f`を含むリクエストがn分でX回）  
  - ベースディレクトリ外アクセス試行検知（正規化後のパスが許可ルート外）

## 是正・防御（Fix / Defense）
- **ASVS DoD（v5.3.2）**: ファイルパス生成に**ユーザ入力を直接使用しない**。内部IDやサーバ側マッピングを使用。やむを得ず使う場合は**厳格バリデーション＋正規化**（`realpath`等）後、**許可ディレクトリ内であることを検証**。  
- 設計：**固定ディレクトリ＋ホワイトリスト**（拡張子/ファイル種別/最大サイズ）。**シンボリックリンク拒否**、**絶対パス拒否**。  
- 実装：`../`やバックスラッシュの単純除去に頼らず、**パス正規化→ベースパス包含判定**。Unicode/二重エンコード/URLデコード後の再検証。  
- 配置：機微ファイルを**Webルート外**に配置。アプリユーザの**最小権限**（R/O）。  
- 運用：WAF/IDSで`../`シグネチャや既知バイパス（`..;%2f`,`....//`等）を**検知**し、**誤検知検証**と**都度チューニング**。  
- テスト：WSTGに準拠した**入力ベクタ列挙**と**手法別検証**を回帰に組込。  

## バリエーション
- エンコード：`%2e%2e%2f`、二重/混合エンコード、UTF-8正規化差異。  
- 区切り文字：`/`と`\`混在、末尾スラッシュ付与、先頭`./`。  
- OS差異：`/etc/*`（Unix系）、`C:\Windows\win.ini`（Windows）。  
- ラッパ/特殊パス（実装依存）：`/proc/self/environ`、シンボリックリンク経由、NULバイト（レガシー）。  
- 起点差異：Cookie/ヘッダ/フォーム/GraphQL引数など**URL以外の入力チャネル**。  

## 再現手順（Minimal Steps）
1) 対象機能を特定（例：`GET /download?path=<file>`）。  
2) **無害ファイル**で検証：`/etc/hostname`または`C:\Windows\win.ini`を相対パスで指定。  
```
GET /download?path=../../../../etc/hostname HTTP/1.1
Host: target.example
```
3) エンコード/区切り差分を試験し、**許可ルート外**の読取可否を確認。  
4) 出力に機微情報が含まれないことを確認しつつ、**ログ/証跡**を採取（時刻JST、req_id、パラメータ、応答サイズ）。  
5) 成果物：再現手順・ログ断片・スクリーンショット（機微は`****`でマスク）をカードに添付。  

## 参照
1. OWASP ASVS 5.0 — File Storage（V5.3.2・パストラバーサル/LFI対策）: https://cornucopia.owasp.org/taxonomy/asvs-5.0/05-file-handling/03-file-storage
2. OWASP WSTG — Testing Directory Traversal File Include: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/01-Testing_Directory_Traversal_File_Include  
3. OWASP WSTG — Testing for File Inclusion: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_File_Inclusion  
4. PortSwigger Web Security Academy — File path traversal: https://portswigger.net/web-security/file-path-traversal  
5. PayloadsAllTheThings — Directory Traversal: https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Directory%20Traversal  
6. HackTricks — File Inclusion/Path traversal: https://angelica.gitbook.io/hacktricks/pentesting-web/file-inclusion
7. MITRE ATT&CK — T1190 Exploit Public-Facing Application: https://attack.mitre.org/techniques/T1190/
