## ガイドライン対応（ASVS / WSTG / PTES / MITRE ATT&CK：毎回記載）
- ASVS
  - 満たす/破れる点：XMLパーサが外部実体を“解決（resolve）”する状態が残っていると、レスポンスに内容が出ない（Blind）設計でも、外向き通信（DNS/HTTP）を通じて成立する。対策は入力フィルタではなく、DTD/外部実体/外部サブセット/参照解決器を無効化し、さらに egress 制御と監視で多層化する（“外へ出られない”前提に依存しない）。
- WSTG
  - XXEは入力検証テストの対象で、Blind/OOB は「レスポンスに反映されないが、外部相互作用（OAST）で成立を観測できる」ことが重要。成立根拠は“外向きの相互作用が発生した”という観測で取る（例：DNS/HTTPコールバック）。:contentReference[oaicite:0]{index=0}
- PTES
  - Vulnerability Analysis：XMLパース地点（API/アップロード/変換/ジョブ）と、外向き通信経路（DNS/HTTP/Proxy）を環境としてモデル化
  - Exploitation：Blindはレスポンス差分が弱いので、OOB（DNS/HTTPの到達ログ）またはエラー誘発（エラーメッセージ差分）で“成立根拠”を証拠化する:contentReference[oaicite:1]{index=1}
  - Reporting：原因は「パーサ設定」「外部参照解決」「egress/監視の不足」を分解し、修正（設定）と検知（ログ）をセットで提案
- MITRE ATT&CK
  - 位置づけ：Blind XXEは“外向き通信”を伴う非同期な脆弱性の一種として整理できる（アプリ応答に出ないがコールバックで検出できる）。OOBコールバックの考え方は非同期脆弱性探索と整合する:contentReference[oaicite:2]{index=2}
  - 目的：内部到達性の足掛かり（SSRF連鎖）、設定・メタデータ探索、環境指紋化へ接続（次ファイル：to SSRF）

---

## タイトル
Blind XXE（OOB）境界モデル：レスポンスに出ない“成立”をどう確定するか

## 目的（この技術で到達する状態）
- Blind XXEを、次の境界モデルで説明・検証できる
  - Parser境界：DOCTYPE/DTDが受理されるか（前ファイル）
  - Resolver境界：外部参照（URI/外部サブセット/外部実体）を解決しに行くか
  - Egress境界：解決のための外向き通信（DNS/HTTP）が可能か（Proxy/Firewall/名前解決含む）
  - 観測境界：アプリ応答に出ない代わりに、OOBログで成立根拠を取れるか:contentReference[oaicite:3]{index=3}
- 実務で次を即断できる
  - “レスポンスに何も出ない＝安全”が誤りである理由（OOBで成立する）を説明できる:contentReference[oaicite:4]{index=4}
  - 攻撃成立の証拠を「外向きDNS/HTTP」「エラー差分」「処理時間差分」で取り、再現性のある報告にできる
  - 修正は「入力バリデーション」ではなく「パーサ設定＋egress設計＋監視」の多層であると示せる:contentReference[oaicite:5]{index=5}

---

## 前提（Blind XXE が“Blind”になる典型）
- XMLはパースされているが、結果がレスポンスに反映されない
  - 例：在庫照会、非同期ジョブ投入、変換/検証だけして破棄、ログ/監査にのみ出力
- 外部実体が解決されても、展開結果が画面に出ない（= in-band の情報流出が見えない）
- それでも、解決（resolve）のために DNS / HTTP 等の外向き相互作用が発生すれば、Blindでも成立を確定できる:contentReference[oaicite:6]{index=6}

---

## 境界モデル（Blind/OOB の“成立根拠”を作る4層）
### 1) Parser境界（前提）：DTD/DOCTYPE を受理できる
- DOCTYPEが拒否されるなら、まずは別経路（アップロード/変換/管理機能）を探す（設定漏れが本命）

### 2) Resolver境界：外部参照を“取りに行く”機能が生きている
- 外部実体（External Entity）や外部サブセット（External Subset）を解決する挙動が残ると、ファイル/HTTP等へ到達し得る
- ここはパーサ実装・設定・ライブラリ差分が大きいので「観測で確定」する（推測しない）

### 3) Egress境界：外向き通信が可能か（DNS/HTTP/Proxy）
- 現実の揺れ（診断で事故る点）
  - DNSは出るがHTTPは出ない（または逆）
  - HTTPはプロキシ経由のみ許可（User-Agentや宛先制限、SNI制約）
  - 直接IP宛は遮断だがドメイン経由は通る、等
- ここは“脆弱性の有無”と“再現性”に直結するため、検証設計（観測点）に含める

### 4) 観測境界：アプリ外で成立を記録できる（OAST）
- OOB観測の核は「攻撃者が制御する観測点に到達ログが残る」こと
- PortSwiggerは Blind XXE の検出として「OAST（out-of-band）による相互作用」を整理している:contentReference[oaicite:7]{index=7}

---

## 観測ポイント（“成立根拠”の取り方：差分で確定する）
### 1) OOB相互作用（DNS/HTTP）の観測
- 期待する根拠：ターゲット環境から、観測点（自分が管理するドメイン等）へ DNS 参照/HTTP リクエストが飛ぶ
- 重要な証拠化
  - タイムスタンプ（要求送信とコールバックの相関）
  - 宛先（どのFQDN/どのパスが叩かれたか）
  - 送信元の特徴（プロキシ経由、UA、SNI、解決順序など）
- PortSwiggerは Blind XXE の検出として OOB 相互作用を明示している:contentReference[oaicite:8]{index=8}

### 2) エラー誘発による“間接的な情報混入”
- Blindでも、エラーメッセージに内部情報が混入することがある（パーサ例外、解決失敗の詳細）
- PortSwiggerは Blind XXE の手段として「エラーを利用して機密を含ませる」方向も整理している:contentReference[oaicite:9]{index=9}
- 実務の取り方
  - エラー本文そのものより、「どの例外が出たか」「設定/ライブラリが何か」を確定する材料として使う（再発防止の修正点を特定）

### 3) タイミング差分（非同期/待ち）
- 外部解決を試みると、名前解決タイムアウトやHTTPタイムアウトで応答が遅くなる場合がある
- ただし誤判定が多い（バックエンド遅延・レート制限・キュー混雑）ため、単独根拠にしない
- 原則：OOBログが取れるなら、タイミング差分は補助に留める

---

## 攻撃者視点での利用（現実寄り：どこが“本命”の経路か）
- 露出しやすい本命経路
  - “変換”や“検証”が絡む機能：インポート、在庫照会、請求書/注文書の取り込み、SVGやOfficeのプレビュー
  - バックエンドジョブ：キュー投入→ワーカー処理（レスポンスに出ない＝Blindになりやすい）
- 攻撃者は「データが返るXXE」より、「内部到達性へ繋がるXXE（OOB/SSRF連鎖）」を重視する
  - そのため、次ファイル（to SSRF）へ地続きに評価する

---

## 次に試すこと（仮説A/B：条件で手が変わる）
### 仮説A：OOBが観測できた（DNS/HTTPが出た）
- 次の一手
  1) どの経路でXMLが処理されたか（API直/アップロード/ジョブ）を特定し、設定漏れのスコープを確定
  2) egress の現実（DNSのみ/HTTP不可/プロキシ必須）を整理し、再現条件として報告に固定
  3) “到達できる＝内部到達性の足掛かり”として、次ファイルの SSRF 連鎖評価へ進む

### 仮説B：OOBが観測できない（何も出ない）
- 次の一手（順番が重要）
  1) DOCTYPEの受理は本当にあるか（別経路で拒否されていないか）
  2) egress 制約を疑う：DNSもHTTPも出ない環境なのか、観測点の取り方が合っていないのか
  3) エラー差分で「解決を試みた痕跡」を探す（例外詳細、タイムアウト）
  4) 同じ機能でも別実装（旧UI/別API/管理画面）を探し、別パーサ経路を当てに行く

---

## 防御設計（Blind/OOB を“設計で封じる”）
- OWASPはXXE対策として「DTD（外部実体）を無効化することが最も安全」と整理している:contentReference[oaicite:10]{index=10}
- 実務の多層（優先順）
  1) パーサ設定：DOCTYPE/DTD無効、外部一般/パラメータ実体無効、外部サブセット無効（“例外経路”も含め全適用）
  2) egress 制御：業務要件がない系統は外向きDNS/HTTPを遮断（ただし“遮断前提”に依存しない）
  3) 監視：外向きDNS/HTTP（特に未知ドメイン、短命FQDN、異常頻度）を検知ルール化
  4) 変換/プレビューの隔離：コンテンツ処理（SVG/Office/XML変換）を別サンドボックスへ分離し、権限・ネットワークを最小化

---

## 手を動かす検証（Labs連動：Blind/OOB を再現して“観測設計”を固める）
- 追加候補Lab
  - `04_labs/02_web/05_input/08_xxe_blind_oob_boundary/`
- 最小構成（現実寄り）
  - 2経路：同期API（レスポンスに出ない）＋ 非同期ジョブ（キュー→ワーカー）
  - 2設定：DTD無効（安全）／DTD有効＋外部参照解決あり（危険）
  - 2ネットワーク：DNSのみ許可／HTTPも許可（差分が“成立根拠”の学習になる）
- 取得する証跡（報告に耐える）
  - アプリ側：リクエストID、処理ログ（ジョブID/ワーカーID）
  - ネットワーク側：DNSクエリログ、HTTPプロキシログ、FWログ（宛先・時刻・結果）
  - 相関キー：`request_id` / `job_id` / `timestamp` を最低限揃える

---

## コマンド/例（例示は最小限：危険な手順のテンプレ化はしない）
~~~~
# 目的：Blind XXEはレスポンスに出ないため、OOB（DNS/HTTP）やエラー差分で“成立根拠”を取る
# 設計メモ：
# - 外向きDNS/HTTPログ（観測点）を用意
# - 同一入力で (1)DOCTYPEなし (2)DOCTYPEあり の挙動差分を取り、
#   「外部参照解決を試みた痕跡（OOB/例外/タイムアウト）」を証拠化する
~~~~

---

## 参考（一次情報に近いもの中心）
- PortSwigger：Blind XXE（OOB相互作用・エラー利用の整理）:contentReference[oaicite:11]{index=11}
- OWASP Cheat Sheet：XXE Prevention（DTD無効化が最も安全、という設計指針）:contentReference[oaicite:12]{index=12}
- OWASP Community：XXE Processing（概要と対策への誘導）:contentReference[oaicite:13]{index=13}
- PortSwigger Research：非同期脆弱性（OOBコールバックで検出する考え方）:contentReference[oaicite:14]{index=14}

---

## リポジトリ内リンク（最大3つまで）
- `01_topics/02_web/05_input_08_xxe_01_parser（doctype_entity）.md`（前提：DOCTYPE受理の境界）
- `01_topics/02_web/05_input_09_ssrf_01_reachability（internal_localhost_metadata）.md`（次段：到達性へ接続）
- `01_topics/02_web/04_api_09_error_model_情報漏えい（例外_スタック）.md`（エラー差分の証拠化）

---

## 次（作成候補順）
- `01_topics/02_web/05_input_08_xxe_03_to_ssrf（internal_reachability）.md`
