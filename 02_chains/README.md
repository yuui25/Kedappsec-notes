# 02_chains（攻撃連鎖カード）運用ガイド

本フォルダは **Webペネトレーションテスト専用** の「攻撃連鎖カード（Chain Card）」を蓄積する場所。  
01_reference（ASVS / WSTG / MITRE ATT&CK / PortSwigger WSA / PayloadsAllTheThings / HackTricks）を根拠に、**入口→横展開→到達点→検知→是正**を最短距離で示す。

- add-chain-card.md : 新規カード作成プロンプト
- collect-chain-card-input.md : `add-chain-card.md`に入れる情報提案プロンプト
---

## 1. 命名規約（ファイル名）

**形式**：`<family>-<variant>[-<engine>][-<pivot>][-<impact>.md]`  

- **family（科目）**：`xss`, `sqli`, `ssrf`, `rsmg`（request-smuggling）, `idor`, `csrf`, `uplo`（upload）, `redir`（open-redirect）, `tpl`（SSTI）, `deser`（反序列化）など
- **variant（手口差分）**  
  - XSS：`ref`（反射）, `stor`（格納）, `dom`  
  - SQLi：`union`, `bool`, `time`, `stack`, `oob`  
  - SSRF：`basic`, `dns`, `blind`, `gopher`  
  - Upload：`ext-bypass`, `mime-bypass`, `polyglot` など
- **engine（必要時のみ）**：`pg`（PostgreSQL）, `my`（MySQL）, `ms`（MSSQL）, `ora`（Oracle）等
- **pivot（横展開の代表語）**：`sess`（セッション奪取）, `priv`（権限昇格）, `tenant`（テナント越境）, `intapi`（内部API）, `cache`, `sso-token`, `lfi`, `rce` など
- **impact（到達点の代表語）**：`admin`, `pii`, `data`, `acct`, `exec`, `takeover` 等

> 可読性のため 3〜5 トークンを目安。長くなる場合は `pivot` または `impact` のどちらかを省略。

**同名衝突時のルール**
1. `pivot` または `impact` を具体化（例：`-sso-token`, `-cache-poison`）
2. 文脈短語を付加：`-api`, `-graphql`, `-spa`, `-django`, `-react` 等
3. それでも衝突する場合のみ `-alt`, `-alt2` を許容（最終手段）

**サンプル**
```
xss-ref-sess-pii.md
xss-stor-tenant-admin.md
xss-dom-sso-token-acct.md
sqli-bool-pg-priv-admin.md
sqli-stacked-ms-rce-exec.md
sqli-oob-ora-dns-data.md
ssrf-meta-intapi-data.md
rsmg-clte-auth-bypass-admin.md   # CL/TE解釈差
```

---

## 2. バリエーションの分割基準（“1脅威＝1ファイル”）

**別ファイルに分ける**
- 入口メカニズムが実質異なる（XSS：反射/格納/DOM、SQLi：UNION/Blind/Time/Stacked 等）
- DB/実行環境により手順・ペイロードが大きく変わる（`-pg`, `-ms`, `-ora` 等を付加）
- 連鎖（Pivot）や到達点（Impact）が別物（例：XSS→Cookie奪取 vs XSS→SSOトークン奪取）

**同一ファイルに併載（「バリエーション」小節で）**
- シンク（sink）や微妙な回避テクの差分（例：`innerHTML` / `location` / `onerror`、CSP回避トリックの違い 等）

---

## 3. カードのテンプレート（手動追加用）

> **注意**：本テンプレは **非破壊・合意範囲内** のステージング実施を前提。  
> 本番系での試験は顧客合意と影響評価を必須とする。

```
# <短いタイトル：family/variant/pivot/impact が直感できる表現>

**Tags:** family:<xss|sqli|ssrf|...> / variant:<ref|stor|dom|union|...> / engine:<pg|my|ms|ora|-> / pivot:<sess|priv|tenant|intapi|...> / impact:<admin|pii|data|exec|...>  
**Refs:** ASVS <ID>, WSTG <章/ID>, ATT&CK <TID>, PortSwigger <topic>, PATT <page>, HackTricks <section>  
**Trace:** created: YYYY-MM-DD / last-review: YYYY-MM-DD / owner: <name or handle>

## 概要
- 対象機能・ユースケース：
- 想定ロール（攻撃者/被害者/第三者）：
- 成功条件（技術/ビジネス）：

## 前提・境界（Non-Destructive）
- 試験環境：<staging / demo / 本番（特別合意時）>
- トラフィック上限・時間帯・禁止操作：
- アカウント/テナント/シークレットの取り扱い：

## 入口（Initial Access）
- 入口の手掛かり（パラメータ/ヘッダ/機能/フロー）：
- 最小PoC（Burp Repeater差分 or cURL最小例）
  - リクエスト例（PIIや鍵は伏せ字）：
  - 期待レスポンス/観測点（差分が分かる最小情報）：
- 参考（01_reference の該当箇所の見出し）：

## 横展開（Pivot / Privilege Escalation）
- 追加条件や連鎖（例：SSRF→メタデータ→内部API→…）：
- 権限境界の跨ぎ方（水平/垂直/テナント越境）：
- 具体的なステップ（3〜5行で要約）：

## 到達点（Impact）
- 技術的到達点（取得できる権限/データ/操作）：
- ビジネス影響（例：個人情報大量閲覧/不正送金/管理操作）：
- 想定深度（標準/拡張/攻撃連鎖完遂）：

## 検知・証跡（Detection & Evidence）
- サーバ/アプリ/認証基盤/リバプロ/キャッシュのログ観測点：
- 相関ID/時刻（TZ）/トレースIDの取り方：
- ATT&CK Data Source / Detection への対応付け：
- WAF/IdP 等の反応（あれば）：

## 是正・防御（Fix / Defense）
- **ASVS要件ID** を満たすための実装指針（DoD化）：
- セキュア設定（入力検証/エンコーディング/認可/CSRF/CSP 等）：
- 回帰防止チェック（ユニット/統合/E2E の観点）：

## バリエーション
- シンク/回避手法/フレームワーク差分（統合で記載すべき軽微な違い）：

## 再現手順（Minimal Steps）
1) <入力点へ到達>  
2) <最小PoC投下>  
3) <観測ポイント確認>  
4) <横展開の最小確認>  
5) <証跡取得（Req/Res, 時刻, 相関ID, スクショ）>

## 参照
- ASVS: <バージョン+ID>
- WSTG: <章とテスト名>
- MITRE ATT&CK: <Txxxx>
- PortSwigger WSA: <ラボ/記事名>
- PayloadsAllTheThings: <ページ名>
- HackTricks: <セクション>
```

---

## 4. 証跡・品質ルール（必須）

- **証跡ファースト**：リクエスト/レスポンス原文、時刻（JST）、相関ID、主要スクショ、関連ログ。  
- **PoCは最小・非破壊**：高負荷や破壊的操作は**禁止**。必要時は **ステージング限定**。  
- **再現性**：テンプレの「再現手順」はコピペで追従できる粒度に。  
- **根拠明記**：ASVS/WSTG/ATT&CK など **一次情報**への対応付けを明確に。  
- **秘匿情報の扱い**：鍵/トークン/個人情報はマスキング。保管せず、サニタイズ版のみ保存。

---

## 5. 執筆フロー（迷ったときの手順）

1. **family / variant** を先に決める  
2. **engine** が手順差を生む場合のみ付与  
3. 代表的 **pivot / impact** を1語ずつ選ぶ  
4. テンプレを埋める（入口→横展開→到達点→検知→是正の順）  
5. 「バリエーション」小節に軽微な差分をまとめ、実質差分が大なら**別カード**に分割  
6. 01_reference の該当ID/見出しを **参照** に記載  
7. レビュー：非破壊性・証跡の十分性・再現性・根拠対応付けを自己点検

---

## 6. 用語の最小語彙（再掲）

- **family**：`xss`, `sqli`, `ssrf`, `rsmg`, `idor`, `csrf`, `uplo`, `redir`, `tpl`, `deser` …  
- **variant（例）**：  
  - XSS：`ref`, `stor`, `dom`  
  - SQLi：`union`, `bool`, `time`, `stack`, `oob`  
  - SSRF：`basic`, `dns`, `blind`, `gopher`  
  - Upload：`ext-bypass`, `mime-bypass`, `polyglot`
- **engine**：`pg`, `my`, `ms`, `ora`  
- **pivot**：`sess`, `priv`, `tenant`, `intapi`, `cache`, `sso-token`, `lfi`, `rce`  
- **impact**：`admin`, `pii`, `data`, `acct`, `exec`, `takeover`

---

## 7. 参考（一次情報・公式系）
- OWASP ASVS: https://owasp.org/www-project-application-security-verification-standard/  
- OWASP WSTG: https://owasp.org/www-project-web-security-testing-guide/  
- MITRE ATT&CK: https://attack.mitre.org/  
- PortSwigger Web Security Academy: https://portswigger.net/web-security  
- PayloadsAllTheThings: https://github.com/swisskyrepo/PayloadsAllTheThings  
- HackTricks: https://book.hacktricks.xyz/

---

この README に沿ってカードを作成し、`02_chains/` 直下に配置してください。命名・テンプレ・証跡・根拠の4点を守れば、レポートの「攻撃シナリオ章」にそのまま流用できます。  
