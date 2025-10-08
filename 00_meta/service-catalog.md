00_meta/20251009-service-catalog-1page-v1.md

# セキュリティサービス一覧（1ページ / Kedappsec-notes）
**作成日**: 2025-10-09（Asia/Tokyo）  
**対象**: Webを中心としたセキュリティ事業で提供する主な業務を、顧客説明・見積り前提で1ページに要約。  
**方針**: 各サービスは一次情報（NIST / OWASP / PTES / OSSTMM / TIBER-EU / DORA 等）に整合。成果物・範囲・想定リスクを明記し、契約前にRoE（Rules of Engagement）を合意する。

---

## A. アセスメント＆ペネトレーション
1) **Web脆弱性診断（VA）**  
   - **目的**: 広く網羅して既知の脆弱性を検出・優先度付け。  
   - **範囲**: 自動スキャン＋限定的手動、誤検知の一次確認。  
   - **成果物**: 発見一覧、技術詳細、優先度、簡易再現手順。  
   - **準拠**: OWASP WSTG（項目参照）。

2) **Webペネトレーションテスト（L1〜L3）**  
   - **目的**: 脆弱性の**実行可能性**と**実害**を検証（非破壊前提）。  
   - **範囲**: L1=診断起点の安全検証 / L2=限定的実利用検証 / L3=複合チェイン・横展開・権限昇格まで（厳格合意）。  
   - **成果物**: PoC（リクエスト/レスポンス/スクショ）、到達可能資産、影響評価、修正提言。  
   - **準拠**: NIST SP800-115（Exploitation/Reporting）、OWASP WSTG、PTES。

3) **ネットワーク／インフラ・ペンテスト**  
   - **目的**: サーバ・OS・NW機器の設定不備・既知脆弱性の実害検証。  
   - **範囲**: 領域合意に基づくリモート/オンサイト試験。  
   - **成果物**: 攻撃経路、影響、是正案、優先度。

4) **API / モバイル / IoT テスト**  
   - **目的**: 対象特有のベクトル（認可、逆コンパイル、ファーム解析等）を評価。  
   - **成果物**: 脆弱性詳細、実機再現手順、修正ガイド。

---

## B. 脅威主導試験＆演習
5) **TLPT / Threat-led Penetration Test（金融等）**  
   - **目的**: **脅威インテリジェンス**に基づく実攻撃シナリオで検知・対応力を評価。  
   - **範囲**: 合意シナリオ、長期チェイン、検知・対応の観測。  
   - **成果物**: シナリオ別達成度、検知遅延、改善計画。  
   - **準拠**: DORAのTLPT要請、TIBER-EU。

6) **レッドチーム / Purple Team 演習**  
   - **目的**: 組織横断の攻撃シミュレーション（人・プロセス・技術）。Purpleは検知改善を共同推進。  
   - **成果物**: 攻撃シナリオ結果、検知/阻止ポイント、運用改善計画。  
   - **準拠**: PTES、OSSTMM（測定・証跡）。

---

## C. 設計・開発・クラウド
7) **設計レビュー / 脅威モデリング**  
   - **目的**: 仕様段階での構造的リスクを低減。  
   - **成果物**: データフロー、STRIDE/GQM等の指摘、設計是正案。

8) **ソースコードレビュー（SAST）**  
   - **目的**: 実装上の欠陥（入力検証、認可、エラーハンドリング等）を検出。  
   - **成果物**: 脆弱箇所、根本原因、修正差分ガイド。

9) **クラウド/コンテナセキュリティ診断**  
   - **目的**: IAM最小権限、メタデータ露出（SSRF）、コンテナイメージ脆弱性の評価。  
   - **成果物**: 誤設定一覧、攻撃経路、ガードレール提案。

---

## D. 運用・対応
10) **MDR / ログ監視・検知改善**  
    - **目的**: 継続監視、チューニング、初動対応支援。  
    - **成果物**: アラート品質指標、ユースケース、改善チケット。

11) **インシデントレスポンス / フォレンジック**  
    - **目的**: 事故対応、証跡確保、根本原因分析、復旧支援。  
    - **成果物**: タイムライン、影響範囲、再発防止計画。

12) **脅威インテリジェンス（CTI）**  
    - **目的**: 攻撃手口・IOC収集と運用への反映（TLPT/RedTeam設計にも利用）。  
    - **成果物**: レポート、検知シグネチャ、優先対策。

---

## E. ガバナンス・プログラム支援
13) **バグバウンティ運用支援**  
    - **目的**: 方針設計、トリアージ、SLAs、報奨設計。  
    - **成果物**: 運用手順、評価基準、公開文書草案。

14) **コンプライアンス/規制対応（DORA / PCI / ISO27001 等）**  
    - **目的**: 規制・標準への整合、監査準備、証跡整備。  
    - **成果物**: ギャップ分析、ロードマップ、各種テンプレ。

---

## 共通ルール（契約前提示事項）
- **スコープ定義**: 対象資産・許可攻撃・禁止事項・試行上限を明記（RoE）。  
- **安全配慮**: 破壊的操作は原則禁止。必要時はバックアップ/ロールバック手順を合意。  
- **証跡管理**: 取得ログ・スクショ・サンプルはアクセス管理下で保管。  
- **成果物標準化**: 要約（経営層向け）＋技術詳細（再現/PoC）＋改善計画を基本セットにする。

---

## 参考（一次情報）
- **NIST SP 800-115**: Technical Guide to Information Security Testing and Assessment  
  https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-115.pdf  
- **OWASP WSTG**: Web Security Testing Guide  
  https://owasp.org/www-project-web-security-testing-guide/  
- **PTES**: Penetration Testing Execution Standard  
  http://www.pentest-standard.org/index.php/Main_Page  
- **OSSTMM**: Open Source Security Testing Methodology Manual  
  https://www.isecom.org/research/osstmm/  
- **TIBER-EU**: Threat Intelligence-based Ethical Red Teaming  
  https://www.ecb.europa.eu/paym/cyber-resilience/tiber-eu/  
- **DORA**: EU Digital Operational Resilience Act（TLPT要件を含む）  
  https://finance.ec.europa.eu/regulation-and-supervision/financial-services-legislation/digital-finance/digital-operational-resilience_en  

---

**Trace**: created=2025-10-09, owner=you, last-review=2025-10-09, source=NIST/OWASP/PTES/OSSTMM/TIBER-EU/DORA