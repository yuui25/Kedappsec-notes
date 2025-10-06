# ASVSに基づく Webペネトレーション開始要件


> 本書は **Kedappsec-notes/01_reference** に収録される「ASVSに基づくWebPT開始要件」の実務ガイドです。  
> 立ち位置：ASVS＝**要件標準**、WSTG＝**検証手順**（READMEの方針に準拠）。

---

## 1. ASVSの立ち位置（なに者か）

- **ASVSは「Webアプリ／Webサービスの技術的セキュリティ**を検証するための**要件標準**。**
  - 目的：市場における「検証範囲と厳密さ（rigor）」のばらつきを**標準化**し、開発・調達・検証で共通の土台にする。  
    参考: OWASP ASVS project「What is the ASVS?」<https://owasp.org/www-project-application-security-verification-standard/>
- **最新版は v5.0.0（2025年5月リリース）**。要件IDは `v<version>-<chapter>.<section>.<requirement>` 形式で引用する（例：`v5.0.0-1.2.5`）。  
  参考: GitHub OWASP/ASVS README（5.0）<https://github.com/OWASP/ASVS>  

> **補足：** ASVSは「要件の**何を満たすか**」を示す標準であり、「**どう試験するか**」はWSTGが担います（後述）。

---

## 2. 検証レベル（L1/L2/L3）とWebPTとの関係

- ASVSは**3つの検証レベル**（L1/L2/L3）で要件の深さを段階化。**L1は“完全にペネトレーションテスト可能”**とOWASP資料に明記。  
  参考（OWASP Developer Guide）: <https://owasp.org/www-project-developer-guide/assets/exports/OWASP_Developer_Guide.pdf>  
  参考（OWASP Dev Guide / SecurityRAT）: <https://devguide.owasp.org/en/03-requirements/04-security-rat/>

**実務指針（最低限）：**  
- **既定はL2**（機微データ／取引扱いがある一般的なB2Bアプリ）。重要資産/規制要件が強い場合は**L3**。限定スコープのPoC検証やローリスク用途のみ**L1**。  
- **契約・合意書で採用レベルを明示**（例：「本WebPTはASVS **L2**準拠の要件達成度をDoDとして判定する」）。

---

## 3. 「Web脆弱性診断」との明確な区別（定義ベース）

| 項目 | Web脆弱性診断（VA） | Webペネトレーションテスト（WebPT） |
|---|---|---|
| 目的 | 既知の弱点の**網羅的把握** | **悪用可能性と連鎖**の実証・影響示威 |
| 手法 | 自動スキャン＋観点点検中心 | **実攻撃シミュレーション**（人手で迂回・連鎖） |
| 標準 | （ASVSは**要件**参照可、WSTGは**観点**） | **ASVSでDoD定義**＋**WSTGで手順**設計 |
| 定義根拠 | （一般定義） | **NIST**: 現実の攻撃を模倣し防御の迂回を試みる試験（SP800-115等） |
| 成果物 | 脆弱性リスト（可能性中心） | **再現手順・証跡・ビジネス影響**／**攻撃連鎖** |

参考: NIST CSRC Glossary「Penetration testing」<https://csrc.nist.gov/glossary/term/penetration_testing>  
参考: OWASP WSTG トップ <https://owasp.org/www-project-web-security-testing-guide/>

---

## 4. WebPT開始要件（受入れ基準／DoDの置き方）

### 4.1 合意事項（前提）
- **非破壊の原則**・禁止行為・負荷上限・営業時間外施行の可否を明記（READMEの品質ルールに準拠）。
- **ASVSレベル（L1/L2/L3）**と**対象機能/役割/環境**（本番/ステージング）を合意。
- **DoD（Definition of Done）**を**ASVS要件IDで規定**：例）`v5.0.0-1.2.5`（OSコマンドインジェクション対策）を満たすこと。  
  参考: ASVS v5 READMEの**要件ID表記規則** <https://github.com/OWASP/ASVS>

### 4.2 DoD（例）
- **必須達成**：採用レベル（L2 など）に**Required**とされる要件ID群が**満たされる／満たされない**の可視化（後述テンプレート）。
- **不達成の扱い**：WebPTで**実際に悪用が成立**（WSTG手順で再現）した場合は、該当要件IDを**未達**と判定し、修正後再検証。

> **要件ID記述例**（ASVS v5 READMEより引用のサンプルID）：  
> `v5.0.0-1.2.5` = 「OSコマンドインジェクション防止を検証」

---

## 5. ASVSの**具体的な使い方**（WebPTワークフローに組み込む）

### 5.1 企画・計画
1) **レベル決定**：アプリの機微度・規制・ビジネス影響に応じ **L1/L2/L3** を選定。  
2) **要件選定**：対象スコープに対応するASVS要件IDリストを作成（例：認証、セッション、アクセス制御、入力処理、暗号、ログなど）。  
3) **試験計画**：各要件IDに対し、**WSTGの該当章**と**Burp操作案**（改変点、成功判定）を紐付ける。  
   - 参考: WSTG トップ <https://owasp.org/www-project-web-security-testing-guide/>

### 5.2 実施（手を動かす）
- **エビデンス化**：各試行を**要件ID**でタグ付けし、**Req/Res・スクショ・ログ**を保存。  
- **実効性の実証**：成立した攻撃は**影響（データ・権限・横移動）**まで記録。  
- **迂回・連鎖**：単発の欠陥を**認可・CSRF・SSRF・ストレージ設定**等に連鎖させ、**業務影響**を具体化。

### 5.3 判定・報告
- **トレーサビリティ表**：要件IDごとに「達成/未達」「証跡リンク」「再現手順（WSTG参照）」を一覧化。  
- **レベル達成**：採用レベル（例：L2）の**必須要件**が満たされているかを合否判定。  
- **契約・調達**：ASVSは**契約要件**として明記できる（ASVS公式「Use during procurement」）。  
  参考: ASVS project page（調達での利用目的）<https://owasp.org/www-project-application-security-verification-standard/>

---

## 6. すぐ使えるテンプレート

### 6.1 DoD（Definition of Done）雛形
```text
本WebPTの受入れ基準（DoD）
- 準拠標準：OWASP ASVS v5.0.0
- 検証レベル：L2
- スコープ：/auth, /admin, /api/*（ロール：user/admin）
- 必須要件：L2でRequiredとされる要件のうち、本スコープに属するID群
- 判定基準：
  (A) すべての対象要件IDが「満たす」
  (B) いずれかの要件IDが未達の場合：修正後に再検証を実施
- 例示ID：v5.0.0-1.2.5（OSコマンドインジェクション対策） 等
```

### 6.2 トレーサビリティ（要件×証跡）
| 要件ID | 章・節（要約） | 試験観点（WSTG参照） | 結果 | 証跡 |
|---|---|---|---|---|
| v5.0.0-1.2.5 | Injection Prevention（例） | コマンド注入の成立有無、パラメタライズ/エンコード | OK/NG | link |

> **表記ルール**：ASVS v5 READMEに従い `v<version>-<chapter>.<section>.<requirement>` で明記。

---

## 7. 参照・根拠（一次資料優先）

- **ASVS 総合ページ**（目的・調達での利用等）  
  <https://owasp.org/www-project-application-security-verification-standard/>  
- **ASVS v5.0.0（最新版）と要件ID表記ルール**  
  <https://github.com/OWASP/ASVS>  （README内「Latest Stable Version - 5.0.0」「How To Reference ASVS Requirements」）
- **「L1は完全にPT可能」等のレベル解説（OWASP系資料）**  
  - OWASP Developer Guide（ASVS解説章 PDF）: <https://owasp.org/www-project-developer-guide/assets/exports/OWASP_Developer_Guide.pdf>  
  - OWASP Dev Guide / SecurityRAT ガイド: <https://devguide.owasp.org/en/03-requirements/04-security-rat/>  
- **WSTG（試験手順の標準）**  
  <https://owasp.org/www-project-web-security-testing-guide/>  
- **NIST（WebPTの定義）**  
  <https://csrc.nist.gov/glossary/term/penetration_testing>

---

### 付録）READMEとの対応
- 「**ASVS＝要件標準**、**WSTG＝検証手順**、**ATT&CK＝連鎖の説明**」という役割分担に準拠。
- 参照フロー（ASVSで深度→WSTGで計画→実施→ATT&CKで連鎖整理）を踏襲。
