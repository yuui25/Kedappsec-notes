# 次情報・標準・教材の要点整理

## クイック比較表
| サイト | 種別 | 主要用途 | 粒度 | 公式 | 備考 |
| :-- | :-- | :-- | :-- | :-- | :-- |
| OWASP ASVS | 標準 | セキュ要件定義/受入基準/DoD | 要件 | [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/) | v5.0.0（2025年時点） |
| OWASP WSTG | 手順 | テスト設計/実施の具体手順 | テストケース | [OWASP WSTG](https://owasp.org/www-project-web-security-testing-guide/latest/) | Living Doc（最新版）<br>[目次翻訳](00_meta/wstg.md) |
| NIST SP 800-115 | 手順 | 計画/合意/報告/品質管理の方法論 | 手順・プロセス | [NIST SP 800-115](https://csrc.nist.gov/pubs/sp/800/115/final) | [目次翻訳](00_meta/NIST_SP_800-115.md)  |
| PTES | 手順 | 実務の各フェーズ標準化 | フェーズ/手順 | [PTES](http://www.pentest-standard.org/index.php/Main_Page) | 技術ガイド有 <br>[目次翻訳](00_meta/PTES.md)|
| OSSTMM | 標準 | 測定重視のテスト方法論/指標 | 測定・ルール | [OSSTMM](https://www.isecom.org/OSSTMM.3.pdf) | [目次翻訳](00_meta/OSSTMM.md) |
| MITRE ATT&CK | カタログ | 脅威起点のTTPマッピング | 戦術・技術 | [MITRE ATT&CK](https://attack.mitre.org/matrices/) | v17.1（2025-04） |
| MITRE CAPEC | カタログ | 攻撃パターン起点の設計/試験 | パターン | [MITRE CAPEC](https://capec.mitre.org/) | List v3.9 |
| MITRE CWE | カタログ | 弱点分類/報告の正規化 | 弱点タイプ | [MITRE CWE](https://cwe.mitre.org/) | List v4.18 |
| NVD | カタログ | CVE詳細/影響/依存関係確認 | CVE/メトリクス | [NVD](https://nvd.nist.gov/) | 米政府DB |
| CVE Program | 仕様 | 脆弱性IDの付番/参照 | 識別子 | [CVE Program](https://www.cve.org/) | 公式ID体系 |
| CVSS v4.0 (FIRST) | 仕様 | 影響評価/優先度判断 | 評価指標 | [CVSS v4.0 (FIRST)](https://www.first.org/cvss/v4-0/) | v4.0（仕様公開） |
| PortSwigger WSA | 教材 | 実践演習/手順学習 | ラボ/手順 | [PortSwigger WSA](https://portswigger.net/web-security) | 無料ラボ多数 |
| OWASP Cheat Sheet Series | 教材 | 実装/運用の要点集 | 設計・実装 | [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/) | 短く実務直結 |
| OWASP Top 10 (Web) | 教材 | リスク認知/優先度共有 | リスク分類 | [OWASP Top 10 (Web)](https://owasp.org/www-project-top-ten/) | 2025 版 |
| OWASP API Security Top 10 | 教材 | API特化リスク認知 | リスク分類 | [OWASP API Security Top 10](https://owasp.org/API-Security/) | 2023 版 |
| PayloadsAllTheThings | ペイロード集 | 侵入/再現/回避の素材集 | ペイロード/手順 | [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) | 実例豊富 |
| HackTricks | 教材 | 攻撃者視点の実務TIPS | 手順・トレードクラフト | [HackTricks](https://book.hacktricks.wiki/) | 幅広い網羅 |
| MDN Web Docs（HTTP/CSP/CORS） | 仕様 | ブラウザ/HTTP挙動の一次根拠 | HTTP仕様/挙動 | [MDN Web Docs](https://developer.mozilla.org/) | CORS/CSP/Headers 参照 |

---

## OWASP ASVS
**これは何か（What）**  
- Webアプリのセキュリティ要件標準（一次情報/標準）。L1〜L3の3レベルで、要件ID（例：V1…V17）として明確に定義。

**使いどころ（When/Why）**  
- 企画/要件定義：非機能要件とDoD（Definition of Done）を明文化。  
- 設計：安全設計のチェックリスト化（章単位で抜け漏れ防止）。  
- 実施：WSTGの試験項目へマッピングして試験範囲を確定。  
- 報告：所見を「ASVS要件ID」基準で紐づけ、説得力を付与。  
- 是正：不足要件→具体対策（設計/実装）へ落とし込み。

**具体的な使い方（How / 手順例）**  
1. スコープに合わせレベル（L1/L2/L3）を選定→対象章の要件IDを抽出。  
2. 各要件をWSTGやCheat Sheetへトレースし、テストケース/設計指針化。  
3. 報告書は「ASVS Vx.x-要件ID」で紐づけ、改善チケットへ転記。

**リンク**  
- 公式：https://owasp.org/www-project-application-security-verification-standard/  
- 関連（DL）：https://owasp.org/www-project-application-security-verification-standard/migrated_content

**メモ**  
- 2025年時点 v5.0.0。要件IDの表記ゆれ防止（例：V5.1.1 など章.節.項）。

## OWASP WSTG
**これは何か（What）**  
- Webアプリのセキュリティテストガイド（一次情報/手順）。章ID（WSTG-…）でテスト内容を体系化。

**使いどころ（When/Why）**  
- 設計：試験観点の洗い出し（攻撃面の網羅）。  
- 実施：手順/前提/判定の具体化（再現性確保）。  
- 報告：再現手順・証跡の標準化。  
- 是正：ASVS要件との対応付けで不足コントロールを明確化。

**具体的な使い方（How / 手順例）**  
1. 対象機能に対応するWSTG章（例：Auth/Session/Business Logic等）をIDで特定。  
2. 章の「テスト目標/手順/参考」を読み、具体のリクエスト/期待応答に落とす。  
3. 証跡（スクショ/HTTPログ）を章IDでタグ付けし報告へ反映。

**リンク**  
- 公式（Latest）：https://owasp.org/www-project-web-security-testing-guide/latest/

**メモ**  
- Living Doc。章ID（WSTG-…）表記でリンク切れ・表記ゆれを防止。

## NIST SP 800-115
**これは何か（What）**  
- セキュリティテスト/アセスメントの技術ガイド（一次情報/方法論）。計画〜実施〜報告の標準プロセス。

**使いどころ（When/Why）**  
- 計画：目的/範囲/合意事項/法的配慮の明文化。  
- 実施：非破壊原則/安全性/証跡管理の徹底。  
- 品質：手戻り防止のレビュー/報告テンプレ規範。  
- 監査：第三者説明用の根拠として提示。

**具体的な使い方（How / 手順例）**  
1. 事前合意テンプレ（範囲/日程/連絡/緊急停止）を本書の構成に沿って整備。  
2. 試験計画から手順・安全基準を派生→WSTG/ASVSを具体化参照。  
3. 報告は所見/リスク/推奨の3分割＋証跡付、是正計画に直結。

**リンク**  
- 公式：https://csrc.nist.gov/pubs/sp/800/115/final

**メモ**  
- 2008 Final。法的・契約・安全運用の指針に有用。

## PTES (Penetration Testing Execution Standard)
**これは何か（What）**  
- ペネトレーションテスト実施標準。事前合意〜情報収集〜脆弱性分析〜侵入〜後処理までの実務指針。

**使いどころ（When/Why）**  
- 計画：スコーピング/見積/合意事項の整理。  
- 実施：各フェーズの期待成果物を定義。  
- 報告：実施範囲・手順・証跡の整合性担保。  
- 教育：新人への標準プロセス教育。

**具体的な使い方（How / 手順例）**  
1. 「Pre-engagement」章で範囲/前提/例外を確定。  
2. 各フェーズをWSTG/ATT&CK/CAPECへトレースして手順を詳細化。  
3. 報告テンプレにPTESフェーズ見出しを流用し読み手負担を軽減。

**リンク**  
- 公式：https://www.pentest-standard.org/  
- 技術ガイド：https://www.pentest-standard.org/index.php/PTES_Technical_Guidelines

**メモ**  
- コミュニティ標準。WSTG/ASVS等との併用で具体性が増す。

## OSSTMM
**これは何か（What）**  
- 運用セキュリティのテスト方法論と測定（RAV等）を定義する一次資料。

**使いどころ（When/Why）**  
- 計画：ルール・オブ・エンゲージメントの厳密化。  
- 実施：測定可能な評価基準で客観性を担保。  
- 報告：RAV/STAR等の定量指標で比較可能に。  
- 監査：外部説明の客観根拠。

**具体的な使い方（How / 手順例）**  
1. テスト境界/相互作用点を特定し、測定軸（Visibility/Access/Trust等）に割当。  
2. 各テストをWSTG手順に落とし、RAV計算で影響度を数値化。  
3. 報告は測定値＋改善策（ASVS/Cheat Sheet）で締める。

**リンク**  
- 公式PDF：https://www.isecom.org/OSSTMM.3.pdf  
- 概要：https://www.isecom.org/research.html

**メモ**  
- v3。測定重視のためWeb単体より包括評価で力を発揮。

## MITRE ATT&CK
**これは何か（What）**  
- 実観測に基づく敵対者の戦術・技術（TTP）知識ベース（一次情報/カタログ）。

**使いどころ（When/Why）**  
- 設計：脅威ドリブンで検知/防御要件を派生。  
- 実施：レッドチーム/シナリオ設計の基盤。  
- 報告：観測TTPを技術ID（T####）で明記。  
- 是正：検知ギャップ/防御カバレッジの可視化。

**具体的な使い方（How / 手順例）**  
1. 企業環境はEnterprise Matrixで該当戦術→技術（TID）を特定。  
2. 技術の検知/緩和をWSTG/ASVS/Cheat Sheetへ接続。  
3. 報告は踏襲TTP（TID）を列挙し検知改善計画へ連結。

**リンク**  
- 公式（Matrix）：https://attack.mitre.org/matrices/  
- バージョン履歴：https://attack.mitre.org/resources/versions/

**メモ**  
- 2025年時点 v17.1。Navigatorで可視化が容易。

## MITRE CAPEC
**これは何か（What）**  
- 攻撃パターンの公開カタログ（一次情報）。設計/試験の観点源。

**使いどころ（When/Why）**  
- 設計：典型攻撃の想定漏れ防止。  
- 実施：テストケースの発想源。  
- 報告：攻撃パターンIDで所見を標準化。  
- 是正：弱点（CWE）/対策へトレース。

**具体的な使い方（How / 手順例）**  
1. 機能に関連するCAPECを検索→攻撃の前提/手順/軽減策を把握。  
2. 内容をWSTG手順やASVS要件に落としテスト案へ反映。  
3. 所見はCAPEC-ID/CWE-ID併記で再発防止に繋げる。

**リンク**  
- 公式：https://capec.mitre.org/  
- CAPEC一覧：https://capec.mitre.org/data/index.html

**メモ**  
- List v3.9。CWE/NVDとの相互参照が強力。

## MITRE CWE
**これは何か（What）**  
- ソフト/ハードの一般的な弱点タイプの体系（一次情報/カタログ）。

**使いどころ（When/Why）**  
- 報告：弱点分類の標準化（CWE-ID）。  
- 是正：根本原因別に対策を整理。  
- 分析：傾向/リスク分布の俯瞰。  
- 教育：設計・実装のアンチパターン学習。

**具体的な使い方（How / 手順例）**  
1. 発見事項をCWE-IDへマッピング（例：CWE-639/IDOR）。  
2. 各CWEの「検出/緩和」節を設計/試験計画に反映。  
3. 管理指標（Top 25など）で優先度付け。

**リンク**  
- 公式：https://cwe.mitre.org/  
- CWEデータ索引：https://cwe.mitre.org/data/index.html

**メモ**  
- List v4.18（2024-11）。所見の再現性・比較性向上。

## NVD (National Vulnerability Database)
**これは何か（What）**  
- CVEの詳細、影響、CPE/依存関係、CVSS等を提供する米国政府DB。

**使いどころ（When/Why）**  
- 実施：使用コンポーネントの脆弱性把握。  
- 報告：CVE/CVSS/影響半径（CPE）を根拠提示。  
- 是正：ベンダー情報/修正状況の追跡。  
- 管理：リスク受付/優先度の定量化。

**具体的な使い方（How / 手順例）**  
1. 依存ソフトのCPEで検索→影響範囲を特定。  
2. 各CVEのCVSS（v3/4）/ベクタを読み、環境スコアを評価。  
3. パッチ/緩和策/回避策をチケット化。

**リンク**  
- 公式：https://nvd.nist.gov/  
- 概要：https://www.nist.gov/programs-projects/national-vulnerability-database-nvd

**メモ**  
- NVDはCVE詳細/メトリクスのハブ。運用の最新状況は公式告知を確認。

## CVE Program
**これは何か（What）**  
- 公開脆弱性に一意ID（CVE-YYYY-NNNN…）を付与する公式プログラム。

**使いどころ（When/Why）**  
- 実施：観測事象の正式な参照点に。  
- 報告：所見/影響/対応のトレーサビリティ。  
- 是正：改修/パッチ適用の進捗管理。  
- コミュニケーション：社内外の共通言語。

**具体的な使い方（How / 手順例）**  
1. CVE-IDで正規化し、NVD/ベンダー告知へ横断リンク。  
2. 内部台帳や報告書のキーとして利用。  
3. 受付/開示プロセス（CNA経由）理解で報告品質を向上。

**リンク**  
- 公式：https://www.cve.org/  
- プロセス概要：https://www.cve.org/about/Process

**メモ**  
- IDの正規参照源。詳細はNVD/ベンダーで補完。

## CVSS v4.0 (FIRST)
**これは何か（What）**  
- 脆弱性の深刻度を標準化評価するスコアリング仕様（一次情報/仕様）。

**使いどころ（When/Why）**  
- 実施：所見の影響評価の一貫性確保。  
- 報告：優先度/意思決定の根拠提示。  
- 是正：環境要因（環境/脅威）で再評価。  
- 管理：SLA/リスク許容の基準化。

**具体的な使い方（How / 手順例）**  
1. Base/Threat/Environmental/Supplementalを読み、該当ベクタを決定。  
2. v4.0仕様の定義どおりにベクタ文字列を生成し報告に併記。  
3. 環境差分はEnvironmentalで反映、優先度に直結。

**リンク**  
- 公式（v4.0）：https://www.first.org/cvss/v4-0/  
- 仕様PDF：https://www.first.org/cvss/v4-0/specification-document

**メモ**  
- v4.0。v3.xとの差異（脅威/補助指標）に留意。

## PortSwigger Web Security Academy
**これは何か（What）**  
- 無料の実践ラボ教材（二次情報/教材）。攻撃手順と防御の双方を学べる。

**使いどころ（When/Why）**  
- 設計/実施：手を動かして再現/検証スキルを獲得。  
- 報告：再現性の高い手順/証跡作成の練習。  
- 是正：防御の観点（ラボ解説）を実装へ反映。  
- 教育：チームの底上げに最適。

**具体的な使い方（How / 手順例）**  
1. テーマ別ラボ（XSS/SSRF/CORS等）を選択→目標を確認。  
2. 手順→Burp/ブラウザ操作→解法をWSTG/ASVSへトレース。  
3. 学びを自社設計指針/チェックリストに転記。

**リンク**  
- 公式：https://portswigger.net/web-security  
- 全ラボ：https://portswigger.net/web-security/all-labs

**メモ**  
- 実務直結。所見テンプレ作成の参考にも最適。

## OWASP Cheat Sheet Series
**これは何か（What）**  
- 各トピックを短く要点化した実装/運用チートシート集。

**使いどころ（When/Why）**  
- 設計：安全デフォルト/設定例の参照。  
- 実装：具体的なコード/設定のヒント。  
- 是正：推奨対策の短文提示に最適。  
- 教育：チームの共通理解醸成。

**具体的な使い方（How / 手順例）**  
1. トピック（CSP/入力検証/認証 等）で検索し該当シートを読む。  
2. 推奨事項をASVS要件/WSTG手順へ対応付け。  
3. 対策案として報告書へ引用（URL付）。

**リンク**  
- 公式：https://cheatsheetseries.owasp.org/  
- Top10連携索引：https://cheatsheetseries.owasp.org/IndexTopTen.html

**メモ**  
- 短く実務的。一次標準へのトレースを併記すると強い。

## OWASP Top 10 (Web)
**これは何か（What）**  
- Webアプリの代表的リスク上位10項のアウェアネス文書。

**使いどころ（When/Why）**  
- 企画：経営/開発への優先課題の共有。  
- 設計：高頻度・高影響の対策優先度決定。  
- 報告：所見の全体位置づけ/非専門家への説明。  
- 教育：新任向けの入門教材。

**具体的な使い方（How / 手順例）**  
1. 現行版（2021）を確認し、該当カテゴリを特定。  
2. カテゴリ→Cheat Sheet/ASVS/WSTGへ展開。  
3. 報告の「背景/影響」でTop 10カテゴリを併記。

**リンク**  
- 公式（2021）：https://owasp.org/Top10/  
- 例：A05 Security Misconfiguration：https://owasp.org/Top10/A05_2021-Security_Misconfiguration/

**メモ**  
- 認知/合意のための入口。技術詳細はASVS/WSTGへ。

## OWASP API Security Top 10
**これは何か（What）**  
- API特有のリスク上位10項（一次情報/教材）。APIの設計/実装に特化。

**使いどころ（When/Why）**  
- 設計：APIの権限/認可/データ露出の整理。  
- 実施：APIテスト観点をWSTG/ASVSに補完。  
- 報告：API所見の背景説明に最適。  
- 是正：API特有のベストプラクティスへ誘導。

**具体的な使い方（How / 手順例）**  
1. 2023版の該当リスク（例：BOPLA等）を確認。  
2. リスク→ASVS V* / WSTG API関連章へマッピング。  
3. 推奨対策はCheat Sheet/MDN（CORS/CSP等）へリンク。

**リンク**  
- 公式トップ：https://owasp.org/API-Security/  
- 2023版一覧：https://owasp.org/API-Security/editions/2023/en/0x11-t10/

**メモ**  
- 2023 版。Web Top10と合わせて全体俯瞰。

## PayloadsAllTheThings
**これは何か（What）**  
- 代表的攻撃のペイロード/バイパス/手順を集約したGitHubリポジトリ。

**使いどころ（When/Why）**  
- 実施：再現/確認の材料として活用。  
- 報告：最小再現手順の参考。  
- 是正：防御観点（フィルタ/設定）の検討。  
- 教育：攻撃者視点の理解強化。

**具体的な使い方（How / 手順例）**  
1. 脆弱性名でディレクトリを検索→代表ペイロードを把握。  
2. 環境に合わせて最小化しWSTG手順と組み合わせて試験。  
3. 報告にはペイロード例と根拠（ASVS/WSTG）を併記。

**リンク**  
- 公式（GitHub）：https://github.com/swisskyrepo/PayloadsAllTheThings  
- Web版：https://swisskyrepo.github.io/PayloadsAllTheThings/

**メモ**  
- 実行はステージング限定/非破壊原則を厳守。

## HackTricks
**これは何か（What）**  
- 多領域（Web/クラウド/内部横展開等）の実務TIPSと手順集。

**使いどころ（When/Why）**  
- 実施：最新の実務ノウハウ取得。  
- シナリオ：横展開/権限昇格の発想補完。  
- 報告：再現手順のヒント。  
- 教育：実戦テクニックの学習。

**具体的な使い方（How / 手順例）**  
1. トピックを検索し、前提/手順/注意点を把握。  
2. 手順はWSTG/ASVSへトレースし標準化（表記/根拠を整える）。  
3. 本番環境では使用せず、検証用に最小化して適用。

**リンク**  
- 公式：https://book.hacktricks.xyz/

**メモ**  
- 非公式のため一次標準へのトレース併記が前提。

## MDN Web Docs（HTTP/CSP/CORSなど）
**これは何か（What）**  
- ブラウザ/HTTPの一次挙動リファレンス。ヘッダ仕様や同一生成元ポリシー等の根拠。

**使いどころ（When/Why）**  
- 設計：CORS/CSP/クッキー属性の正しい理解。  
- 実施：期待挙動/エラーの根拠確認。  
- 報告：仕様に基づく是正案の提示。  
- 教育：HTTP/ブラウザ基礎の共有。

**具体的な使い方（How / 手順例）**  
1. 該当ヘッダ/機能ページ（CORS/CSP/Set-Cookie等）を参照。  
2. 実装指針（例：Strict CSP）をCheat Sheet/ASVSへマッピング。  
3. 期待挙動と現状差分を根拠URL付きで報告。

**リンク**  
- CORSガイド：https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS  
- CSPガイド：https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CSP  
- Set-Cookie仕様：https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Set-Cookie

**メモ**  
- 最新ブラウザ挙動に追随。英語版が最も更新が速い傾向。

---
