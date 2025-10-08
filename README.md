# Kedappsec-notes
- Webペネトレーション（WebPT）用のナレッジ。
- OWASP/WSTG・ASVS・MITRE ATT&CK 等の一次資料を核に、攻撃の入口から連鎖（横展開）までを実務向けに整理する。**(予定)**
- 概要や攻撃の入り口発見に使い、詳細については各外部サイトへリンク。

**!!!!現在作成途中のため、別途各資料の確認は必要!!!!**

## 目的とスコープ
- **診断の結果/初期情報から「次アクション」を即決**（どこを深掘るか、何を試すか）
- **攻撃連鎖（Chain Card）で横展開を設計**（証跡・根拠つきで再現可能）
- **一次資料で裏取り**（WSTG/ASVS/ATT&CK等への明示リンク）
> **対象**：Webアプリ

## 使い方
1. **全体像**を掴む → [`01_reference/00_webpt-integrated-flow.md`](01_reference/00_webpt-integrated-flow.md)  
2. **連鎖カードの作法**を知る → [`02_chains/README.md`](02_chains/README.md)  
3. **入口候補を洗い出す** → [`03_matrices/attack_by_function.md`](03_matrices/attack_by_function.md)  
4. **各脆弱性について読む/追加する** → [`02_chains/`](02_chains/)  
5. **根拠を付す** → ASVS/WSTG/ATT&CK などを該当IDで紐づけ

## フォルダ別ガイド
### 00_meta（運用方針・注意事項）
- [`00_meta/README.md`](00_meta/README.md)：命名規約・非破壊原則・参照ルール  
- `00_meta/archive/`：旧文書の保管  

### 01_reference（一次資料の要点と使い分け）
- **入口**：[`01_reference/README.md`](01_reference/README.md)  

### 02_chains（攻撃連鎖カード）
- **運用ガイド**：[`README.md`](02_chains/README.md) / **候補**：[`カード追加候補リスト.md`](02_chains/カード追加候補リスト.md)  
- **公開中カード（抜粋）**  
  - IDOR：[`IDOR/idor-basic-priv-admin.md`](02_chains/IDOR/idor-basic-priv-admin.md)  
  - Path Traversal：[`Path Traversal/path-basic-lfi-data.md`](02_chains/Path%20Traversal/path-basic-lfi-data.md)  
  - SSRF：[`SSRF/ssrf-meta-intapi-data.md`](02_chains/SSRF/ssrf-meta-intapi-data.md)  
  - XSS：[`XSS/xss-ref-sess-pii.md`](02_chains/XSS/xss-ref-sess-pii.md)

### 03_matrices（入口マトリクス）
- 機能→攻撃の入口：[`attack_by_function.md`](03_matrices/attack_by_function.md)

### 05_prompts（生成・保守プロンプト）
- 生成：[`gen/add-chain-card.md`](05_prompts/gen/add-chain-card.md) / [`gen/collect-chain-card-input.md`](05_prompts/gen/collect-chain-card-input.md)  
- 保守：[`maintain/update-attack_by_function-from-card.md`](05_prompts/maintain/update-attack_by_function-from-card.md)

## 導線
1. このREADME → 目的と構成を把握  
2. [`01_reference/00_webpt-integrated-flow.md`](01_reference/00_webpt-integrated-flow.md) → 進め方の型  
3. [`02_chains/README.md`](02_chains/README.md) → カードで連鎖の読み方を理解

## 参考
1. OWASP Web Security Testing Guide (WSTG) – https://owasp.org/www-project-web-security-testing-guide/latest/  
2. OWASP ASVS – https://owasp.org/www-project-application-security-verification-standard/  
3. MITRE ATT&CK – https://attack.mitre.org/  
4. PortSwigger Web Security Academy – https://portswigger.net/web-security  
5. PayloadsAllTheThings – https://github.com/swisskyrepo/PayloadsAllTheThings  
6. HackTricks – https://angelica.gitbook.io/hacktricks

---
