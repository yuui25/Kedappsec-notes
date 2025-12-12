# 02_chains（攻撃連鎖カード）運用ガイド

本フォルダは **一次資料（ASVS / WSTG / MITRE ATT&CK など）にたどり着くための「道標」** を提供するための参照集です。  
各カードは一次情報へのリンクを備えた **最短の実務フロー（入口 → 横展開 → 到達点 → 検知 → 是正）** を示し、詳細の正当性は必ず一次資料で確認してください。

---

## 1. クイックスタート（カードを「使う」流れ）

1) **入口を知りたいとき（機能から攻撃を引く）**  
   - [`03_matrices/attack_by_function.md`](../03_matrices/attack_by_function.md) を開き、対象機能（例：ログイン、検索、ファイルアップロード）から**想定される攻撃の入口**を特定。  
   - 入口に紐づく **カード名（family/variant/pivot/impact）** を確認し、該当カードを `02_chains/<カテゴリ>/` から開く。

2) **既に兆候を見つけたとき（脆弱性から深掘る）**  
   - 見つけた兆候に対応する **ファミリ（例：XSS, IDOR, SSRF, Path Traversal など）** のカードを開く。  
   - カードに沿って **最小・非破壊の PoC → 証跡取得（Req/Res 原文・時刻・相関ID） → 一次資料で根拠確認** の順で進める。

3) **カードが見当たらない／粒度が合わないとき**  
   - 先に一次資料（`01_reference`）で要件・試験観点を確認。  
   - その上で **新規カードを追加**（作り方は「2. カード追加方法（詳細）」を参照）。

---

## 2. カード追加方法（詳細）

- **候補確認 → 入力整理/自動生成（分岐）**  
  1. まず **候補リスト** を確認：[`00_meta/カード追加候補リスト.md`](../00_meta/カード追加候補リスト.md)  
  2. 次のいずれかで進める：  
     - **A. 候補リストに該当がある／記載内容が明確** → 直接 **本文生成** へ  
       - 本文生成：[`05_prompts/gen/add-chain-card.md`](../05_prompts/gen/add-chain-card.md)  
     - **B. 候補がない／内容が曖昧** → まず **入力整理** を行い不足を埋める  
       - 入力整理：[`05_prompts/gen/collect-chain-card-input.md`](../05_prompts/gen/collect-chain-card-input.md)  
       - 整理後に **本文生成** へ

- **配置先**  
  - 脆弱性ファミリごとのサブフォルダに配置（例：`02_chains/IDOR/`、`02_chains/XSS/`）。適切なら新規カテゴリ作成可。

- **命名（要点のみ。詳細は別ファイル）**  
  - 形式：`<family>-<variant>[-<engine>][-<pivot>][-<impact>.md]`（3〜5 トークン推奨）  
  - 例：`xss-ref-sess-pii.md` / `idor-basic-priv-admin.md` / `ssrf-meta-intapi-data.md`  
  - 詳細：[`00_meta/naming-glossary.md`](../00_meta/naming-glossary.md)

- **本文の粒度**  
  - 読み手が **5〜10 分で再現** できることを最優先。PoC はステージング想定の **最小差分** とし、破壊的操作は禁止。

- **根拠の紐づけ**  
  - 本文または末尾「参照」で **ASVS 要件ID / WSTG テスト / ATT&CK / PortSwigger / PayloadsAllTheThings / HackTricks** を明示。

- **品質・証跡**  
  - **Req/Res 原文・時刻（JST）・相関ID** を保存。個人情報・鍵はマスキング。  
  - **再現性**：コピペで追従できる手順／前提条件を明記。  
  - **非破壊原則**：高負荷・破壊的・不許可領域へのアクセスは記載不可。

- **作成後の反映**  
  - マトリクス更新：[`05_prompts/maintain/update-attack_by_function-from-card.md`](../05_prompts/maintain/update-attack_by_function-from-card.md) を実行し、  
    [`03_matrices/attack_by_function.md`](../03_matrices/attack_by_function.md) に新カードを反映。

> **補足**：  
> `naming-glossary.md` と `カード追加候補リスト.md` は **`00_meta/` に置く**。将来的に量が増えたら、`00_meta/glossary/`（用語集系）と `00_meta/backlog/`（候補・ToDo系）に分割してもよい。

---

## 3. 命名規約（要点）

**形式**：`<family>-<variant>[-<engine>][-<pivot>][-<impact>.md]`（3〜5 トークン推奨）  
- `family` 例：`xss`, `sqli`, `ssrf`, `idor`, `path` …  
- `variant` 例：`ref`, `stor`, `dom`, `basic`, `union` …  
- `engine` 例：`pg`, `ms`, `my`, `ora` …（必要時のみ）  
- `pivot` 例：`sess`, `priv`, `tenant`, `intapi`, `lfi`, `rce` …  
- `impact` 例：`admin`, `pii`, `data`, `exec` …  

> 詳細は [`00_meta/naming-glossary.md`](../00_meta/naming-glossary.md) を参照。

---

## 4. カードの分割ルール（**1 目的 = 1 カード**）

**読者が“ゴールに一直線”で到達できる単位で分ける。**

- **別カードに分ける**  
  - 入口メカニズムが実質別物：XSS（反射/格納/DOM）、SSRF（基礎/メタデータ/Blind）  
  - 実行環境で手順が大きく変わる：`-pg` と `-ms` で PoC が大幅相違  
  - 連鎖や到達点が別の物語：例）「XSS→Cookie 奪取」 vs 「XSS→SSO トークン奪取」

- **同一カードに併載**  
  - シンクや回避テク等の **微差**（例：`innerHTML` / `onerror`、CSP 回避バリエーション）

---

## 5. 証跡・品質ルール（必須）

- **証跡ファースト**：Req/Res 原文、時刻（JST）、相関ID、関連ログ。  
- **PoC は最小・非破壊**：ステージング前提。高負荷・破壊的手順は記載不可。  
- **再現性**：前提・手順・期待結果を明確化。  
- **一次資料優先**：カードは道標。最終判断は ASVS / WSTG など **一次情報** に従う。  
- **秘匿情報**：鍵/トークン/個人情報は必ずマスキング。

---

## 6. プロンプトへのリンク

- **新規カード生成**：[`05_prompts/gen/add-chain-card.md`](../05_prompts/gen/add-chain-card.md)  
- **入力項目の整理支援**：[`05_prompts/gen/collect-chain-card-input.md`](../05_prompts/gen/collect-chain-card-input.md)  
- **マトリクス更新**：[`05_prompts/maintain/update-attack_by_function-from-card.md`](../05_prompts/maintain/update-attack_by_function-from-card.md)

---

## 7. 参考（一次情報・公式系）

- **OWASP ASVS**：https://owasp.org/www-project-application-security-verification-standard/  
- **OWASP WSTG**：https://owasp.org/www-project-web-security-testing-guide/  
- **MITRE ATT&CK**：https://attack.mitre.org/  
- **PortSwigger Web Security Academy**：https://portswigger.net/web-security  
- **PayloadsAllTheThings**：https://github.com/swisskyrepo/PayloadsAllTheThings  
- **HackTricks（公式 Book）**：https://book.hacktricks.xyz/
