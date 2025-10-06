
# MITRE ATT&CKに基づく Webペネトレーション開始要件

> 目的：**攻撃者の行動様式（ATT&CK）に沿って、実際に“侵入可能か・どこまで到達できるか”を実証**する。  
> 対比：**脆弱性診断＝“あり得る欠陥の列挙”**、**Webペネ＝“実際の侵入経路と影響（連鎖）を実証”**。NIST SP800-115も“評価（VA）”と“侵入テスト（PT）”を区別して定義している。

---

## 1) 事前合意（Rules of Engagement / スコープ）
- **対象と深度**：Webアプリ（SPA/SSR/SaaS含む）。“初期侵入→権限昇格→横移動→影響”まで、**攻撃連鎖の実証**を目的に設定。  
- **禁止事項**：DoS/データ破壊/大量送信などの**高リスク手法は除外 or 模擬**。  
- **環境情報・アカウント**：テスト用/検証用の**有効アカウント（一般/管理）**、メール受信手段、2FAの扱い（本番or模擬）を合意。  
- **監査証跡**：アプリ/リバプロ/IdP/メール/サーバの**ログ保全**、セッションIDの取り扱い手順。  
- **安全装置**：緊急停止連絡系、時間帯、IP許可、レート制御の設定。  
- **成果物**：**ATT&CKマッピング付き**の攻撃経路図、再現手順、PoC、ログ・キャプチャ、是正策と**優先度**。  

---

## 2) アプローチ（ATT&CKタクティクスに沿った最小セット）

### A. Reconnaissance（偵察）
- 能動スキャン、被害者情報収集、公開資産の洗い出し。Webペネでは**攻撃面の仮説づくり**に直結。

### B. Resource Development（侵攻準備）
- フィッシング用ドメイン/送信手段、使い捨てアカウント等（必要時）。Webのテストでも準備段階の合意が必要。

### C. Initial Access（初期侵入）
- **T1190: Exploit Public-Facing Application**（公開Webの脆弱性・設定不備を実利用）  
- **T1078: Valid Accounts**（正規アカウントの悪用。クラウド/SaaS含む）

### D. Credential Access / Defense Evasion（認証・回避）
- **T1539: Steal Web Session Cookie**（セッションCookieの窃取→乗っ取り）  
- **T1550.004: Use Alternate Authentication Material: Web Session Cookie**（盗んだCookieで再認証省略・MFA回避など）

### E. Discovery / Privilege Escalation / Lateral Movement
- IDOR/強制ブラウジング、テナント越境可否などを**攻撃者行動として実証**。

### F. Impact（影響）
- データ閲覧/改ざん/機密機能の不正実行など、**ビジネス影響**に結びつく“最終状態”を定義（破壊行為は除外して模擬）。

---

## 3) 進め方（チェックリスト）
- **計画**：対象・非対象、禁止行為、深度、アカウント/MFAの扱い、ログ保全、停止条件、成果物テンプレを**書面合意**（NIST SP 800-115等の枠組みを参照）。  
- **実施手順（概略）**：  
  1. 偵察 → 2. 初期侵入（T1190/T1078等） → 3. セッション/認証回避（T1539等） →  
  4. 権限境界の突破（IDOR等） → 5. 影響実証（最小限） → 6. ログ相関・再現性確認。  
- **報告**：**ATT&CKマトリクスに重ねた攻撃経路図**、再現手順、是正優先度、診断（VA）との差分を明記。

---

## 4) 「Web脆弱性診断」との明確な区別（要点）
- **目的**：VAは“何が脆弱か”の列挙、Webペネは“どう突破されたか/どこまで行けるか”の実証。  
- **手法**：VAはツール＋限定的手動、Webペネは手動主体で攻撃連鎖を辿る。  
- **アウトプット**：VAは所見リスト、Webペネは侵入ストーリー＋影響＋再発防止策。

---

## 5) 代表的なATT&CK技術（Webコンテキスト）と対応付け例
- **T1190: Exploit Public-Facing Application** — 認可不備やCSRF保護欠如の悪用。  
- **T1078 / T1078.004: Valid Accounts** — 貸与アカウントの権限逸脱検証。  
- **T1539 / T1550.004: Cookie窃取→セッション再利用/MFAバイパス**。  
- **T1185: Supply Chain Compromise（ブラウザ/拡張含む間接的手法）** なども文脈によって考慮。

---

## 付録：実務上の注意点
- 法的・契約的合意を最優先にすること（実害発生の責任回避）。  
- 高リスクな手法（DoS、実データ削除等）は模擬で代替し、成果物で**模擬条件**を明記すること。  
- 証跡（ログ・パケットキャプチャ・スクリーンショット）は時刻同期（NTP）、ハッシュ保存などの運用を明確に。

---

## 参考・一次資料（主な参照先）
- MITRE ATT&CK（Enterprise） — https://attack.mitre.org  
- NIST Special Publication 800-115 — https://csrc.nist.gov/publications/detail/sp/800-115/final  
- OWASP Web Security Testing Guide (WSTG) — https://owasp.org/www-project-web-security-testing-guide/latest/  
- OWASP ASVS（参考） — https://owasp.org/www-project-application-security-verification-standard/  
- （必要であれば、これらに加え攻撃技術別のCWE/CVEやPortSwigger記事などを追加可）

---

## 出力形式
- 本ファイルは**ダウンロード可能なMarkdown（.md）**です。社内共有やレポートテンプレートのベースとしてご利用ください。
