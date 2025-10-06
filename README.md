# Kedappsec-notes
Webペネトレーション（WebPT）に特化したナレッジ。OWASP/WSTG・ASVS・MITRE ATT&CK 等の一次資料を核に、攻撃の入口から連鎖（横展開）までを実務向けに整理する。**(予定)**

## 目的とスコープ
- **目的**：脆弱性診断の結果や限られた初期情報から、WebPTの**次アクション**を素早く導くための参照集を作る。
- **対象**：Webアプリ。  
- **非対象**：ネットワーク機器・インフラ構成・モバイルバックエンド・WAF/IdP連携・サブドメイン全探索 等。

## 使い方（ユースケース）
1) **診断→PTへの踏み込み**  
   例：「IDOR検出→対象機能×スタックから横展開仮説→最小安全PoC→影響評価→対策案」  
2) **URLだけ渡された初動**  
   例：「機能ごとマトリクス→入口候補→典型連鎖→必要証跡チェック」  
3) **根拠を示して説明**  
   一次資料（WSTG/ASVS/ATT&CK等）へ**明示リンク**し、判断根拠を残す。

## フォルダ構成
```
Kedappsec-notes/
├─ 00_meta/                          # 方針・命名規約・注意事項
│  └─ README.md
├─ 01_reference/                     # 一次資料の要点と使い分け
│  ├─ index.md
│  ├─ wstg.md / asvs.md / attack-mitre.md / hacktricks.md / payloadsallthethings.md
├─ 02_chains/                        # 攻撃連鎖（入口→横展開→到達点→影響→対策）
│  └─ YYYYMMDD-<short>-v1.md
├─ 03_matrices/                      # 脆弱性×技術スタック×Web機能の入口マトリクス
│  ├─ attack_by_function.md
│  ├─ attack_by_stack.md
│  └─ stack_by_function.md
├─ 04_poc/                           # PoC 置き場
│  └─ README.md / idor/ / xss/ / ssrf/ ...
└─ 05_prompts/                       # 生成/保守用プロンプト
   ├─ README.md
   ├─ gen/ (add-vuln-card.md / expand-attack-chain.md / build-matrix-entry.md / write-poc-template.md / ...)
   └─ maintain/ (matrix-consistency-check.md / reference-sync.md / repo-governance.md / ...)
```

## 参考
1. OWASP Web Security Testing Guide (WSTG) – https://owasp.org/www-project-web-security-testing-guide/latest/  
2. OWASP ASVS – https://owasp.org/www-project-application-security-verification-standard/  
3. MITRE ATT&CK – https://attack.mitre.org/  
4. PortSwigger Web Security Academy – https://portswigger.net/web-security  
5. PayloadsAllTheThings – https://github.com/swisskyrepo/PayloadsAllTheThings  
6. HackTricks – https://book.hacktricks.xyz/

---
