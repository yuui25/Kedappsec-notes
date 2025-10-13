# 01_reference — 参照ハブ（README）

このフォルダは、Webペネトレーションテスト（WebPT）を**一次情報中心**で実施・説明するための“根拠集”です。各ドキュメントの**役割／使いどころ／読み順**を明確化し、以降のチェイン作成（`02_chains`）、マトリクス整備（`03_matrices`）に接続します。

---

## 現在の構成（2025-10 時点）

- **01_webpt-reference-quick-guide.md**  
  各一次情報／標準／教材の**「これは何か」「使いどころ」「具体的な使い方」**を簡潔比較。  
  *使いどころ*: 全体像の提示／合意形成の前提合わせ、関係者教育の最初の一歩。

- **02_mapping_merged.md**  
  **ASVS v5.0 → WSTG マッピング**を起点に、要件ごとに「具体的な攻撃例」「精査方法（検出理由）」「参照リンク」を併記した**実務テーブル集**（範囲外の扱い理由も明記）。  
  *使いどころ*: スコープ確定、試験観点の洗い出し、レポートの根拠づけ。

- **03_web-pentest-providers.md**  
  国内外の**WebPT提供企業**を収集。多くの企業が**脆弱性診断（VA）とPTを別メニュー**で提示している事例をリンクで示す。  
  *使いどころ*: **期待値調整／社内外ベンチマーク**。

- **04_public-web-pentest-reports.md**  
  **実査ベースの公開PTレポート**（Cure53, ROS ほか）と匿名サンプル/アグリゲータへのリンク。**成果物の粒度（再現手順・影響評価・是正提言）**を合わせる参考。  
  *使いどころ*: 報告書の**構成・深度のキャリブレーション**、テンプレ見直し。

- **05_service-catalog.md**  
  事業視点の**1ページ・サービスカタログ**。VA/PT（L1〜L3）・TLPT/Red/Purple・レビュー/コード/クラウド・運用/IR・ガバナンスまで、**目的／範囲／成果物／準拠**を要約。契約前の**RoE（合意事項）**も明記。  
  *使いどころ*: 顧客説明・見積り・契約前合意の**定型**。

- **06_VulnerabilityAssessment-vs-WebPenetration.md**  
  **Web脆弱性診断（Vulnerability Assessment）とWebペネトレーションテスト**の違いを、表＋短い具体例で整理。横展開（Pivot）や影響実証の要点を**一次情報リンク付き**で提示。  
  *使いどころ*: **用語・境界の共通認識づくり**、見積り／スコープ線引きの事前合意。

---

## 推奨の読み順（最短ルート）

1. **用語と境界をそろえる**：`06_VulnerabilityAssessment-vs-WebPenetration.md`  
2. **サービスの枠組みを決める**：`05_service-catalog.md`  
3. **根拠体系を共有する**：`01_webpt-reference-quick-guide.md`  
4. **試験観点を具体化する**：`02_mapping_merged.md`（ASVS→WSTG、攻撃例・精査方法つき）  
5. **期待値と成果物の粒度を合わせる**：`03_web-pentest-providers.md` → `04_public-web-pentest-reports.md`

---

## 運用ルール（更新・品質）

- **一次情報優先**：ASVS/WSTG/ATT&CK/NIST 等の更新を定例点検。リンク切れは都度修正。  
- **範囲の明確化**：`02_mapping_merged.md` の「範囲外」理由に準拠し、**設計/運用のみで検証困難な項目**はWebPT対象外として明記。  
- **非破壊の原則**：本番環境は合意範囲・最小限で検証。DoS/大量送信等は原則禁止（RoE準拠）。  
- **再利用性**：`04_public-web-pentest-reports.md` の構成/深度を**報告テンプレ**に反映。`03_web-pentest-providers.md` は**期待値調整**の引用定番とする。  
- **用語統一**：VA/PTの定義は `06_VulnerabilityAssessment-vs-WebPenetration.md` に準拠。

---

## 用語の手早い区別（VA と PT）

| 区分 | 目的 | 手段 | 成果物の核 |
|---|---|---|---|
| **VA（Vulnerability Assessment／脆弱性診断）** | 既知脆弱性の**網羅検出** | スキャン＋定型手動 | 発見一覧・優先度・再現要旨 |
| **PT（Penetration Testing）** | **到達可能性**と**実害**の検証 | 手動チェイン・横展開 | PoC（Req/Res/スクショ）・影響評価・是正計画 |

> **詳細版・一次情報リンク**は `06_VulnerabilityAssessment-vs-WebPenetration.md` を参照。

---

## 関連フォルダとの接続

- **02_chains**：`02_mapping_merged.md` の観点→**攻撃連鎖カード**化（入口／横展開／到達点／対策）。  
- **03_matrices**：Web機能×技術スタック×脆弱性の**入口マトリクス**に反映。
