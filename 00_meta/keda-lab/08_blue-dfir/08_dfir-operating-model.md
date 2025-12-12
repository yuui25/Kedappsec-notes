<<<BEGIN>>>
# 08_blue-dfir / BASICS08-08 DFIR運用モデル（Blue/Red/Clientの役割・Evidence Workshop・例外処理・PII/法務最小ルール）

## 1. 目的（本編STEPへの地続き）
本ファイルは、BASICS08-01〜07 を “現場運用” に落とす。
DFIRの品質はテンプレだけでは上がらず、**情報授受と合意形成（誰が・いつ・何を）** が揃って初めて
Hop別の yes/no/unknown が Evidence で固定される。

- Blue/Red/Client の役割と責任分界を固定し、REW/CC（STEP5-14）を実行可能にする
- Evidence Workshop（ログ抽出会）の進め方を固定し、unknown を減らす
- 例外（権限不可/保持切れ/秘密情報/第三者）を “決め” で処理し、停滞を避ける
- PII/法務/監査の最小ルールを明文化し、ログ提供を進める（不安で止まるのを防ぐ）

接続先：
- STEP5（05_pentest-chain）：DFIR章を “合意済み証跡” として成立させる
- BASICS08-06（REQ仕様）：要求（REQ-###）を運用で回す
- BASICS08-07（トラッカー）：進捗と状態遷移を回す
- 06_os-network-protocol：観測点の穴（unknown）の説明責任を果たす

---

## 2. 役割モデル（固定）
### 2-1. 主要ロール
- Blue（Defender）：ログ保持/抽出/検知/運用の主体（CSIRT/SOC/基盤運用）
- Red（Tester）：攻撃チェーン側の事実（実施内容/時刻/対象/PoC）を提示し、相関の要件を定義
- Client Owner（System Owner）：提供可否/範囲/期限の意思決定（Accountable）
- Data Steward（法務/監査/個情）：PII・機密・第三者情報の取り扱い方針を決める
- Platform Admin（IdP/Cloud/EDR/SIEM）：抽出・設定変更の実務担当（Responsible）
- PM/Engagement Lead：期限と合意を管理（トラッカー運用責任）

### 2-2. ロール別アウトプット（固定）
- Blue：
  - raw/normalized/note（Evidence Pack互換）
  - 監査設定・保持設定の証跡（REQ-ARC）
  - 既存検知（アラート/チケット）情報の提示
- Red：
  - Run Log（いつ・誰が・どこに・何をした）
  - 期待する相関キー（request_id/correlation_id等）
  - “観測できるはず/できないはず” の仮説（ただし結論はEvidenceで確定）
- Client Owner：
  - REQ受理/却下の判断、期限確定、提供範囲の承認
- Data Steward：
  - マスキング方針、転送経路、アクセス制御（PII/機密）
- Platform Admin：
  - 実際の抽出/連携/保持変更/検知ルール実装

---

## 3. RACI（運用モデル固定：DFIR活動別）
活動単位でRACIを固定し、議論を減らす。

### 3-1. 活動×RACI（要約）
- ログ抽出（REQ-LOG）：R=Platform Admin / A=Client Owner / C=Red / I=Blue Lead
- 権限付与（REQ-ACC）：R=Client Owner or Platform Admin / A=Client Owner / C=Data Steward / I=Red
- 保持変更（REQ-RET）：R=Platform Admin / A=Client Owner / C=Blue / I=監査
- SIEM連携（REQ-INT）：R=Platform Admin / A=Client Owner / C=Blue / I=Red
- 相関キー設計（REQ-COR）：R=Platform+App Owner / A=Client Owner / C=Red / I=Blue
- 検知実装（REQ-DET）：R=Blue / A=Blue Lead / C=Red / I=Client Owner
- 設定証跡提示（REQ-ARC）：R=Platform Admin / A=Client Owner / C=Data Steward / I=Red

---

## 4. Evidence Workshop（ログ抽出会）の設計（固定）
### 4-1. Workshopの目的（固定）
- unknown の主要因（Trigger-A〜E）を “その場で” 切り分ける
- P0（Hop2/5/7）を最優先に Evidence ready へ持っていく
- 抽出経路（A/B/C）と担当を確定し、REQを accepted にする

### 4-2. 参加者（最小）
- Client Owner（A）
- Platform Admin（R）
- Blue Lead（C/R補助）
- Red Lead（C）
- Data Steward（必要に応じて）

### 4-3. 事前準備（固定）
- Red：Run Log（hop/event_scope/time_window/対象）を事前共有
- Blue：ログソース一覧（BASICS08-02のPrimary/Secondary）が現環境で有効か確認
- トラッカー：P0 REQ 候補をドラフト（REQ-###仮採番）

### 4-4. 当日のアジェンダ（固定：90分想定）
1) 目的確認（unknown削減、P0優先）10分
2) Hop2（認証）Evidence作成 20分
3) Hop5（権限）Evidence作成 20分
4) Hop7（監査停止/痕跡）Evidence作成 20分
5) 残りHop（Hop1/4/6）優先付け 10分
6) REQ accepted/期限/RACI確定 10分

### 4-5. 成果物（必須）
- accepted REQ-###（最低3件：Hop2/5/7）
- Evidence drafted（E-###採番し、raw/normalized/note の作成担当確定）
- blocked の場合は trigger と理由（log_not_available を根拠化）

---

## 5. 例外処理（固定：止めないための決め）
### 5-1. 権限不可（Trigger-C）
- 決め：代替案をその場で確定する  
  例：管理者の画面共有で抽出（Aルート）→ 後日API権限（REQ-ACC）を正式化
- 決め：抽出担当の明確化（誰が取れるか不明）は “blocked” として放置禁止  
  → REQ-OPS で担当決めを要求

### 5-2. 保持切れ（Trigger-B）
- 決め：取れない期間は unknown を確定し、以後の期間で再発防止（REQ-RET）へ移る
- 決め：アーカイブ/バックアップがある可能性を確認（Secondaryの探索）
- 決め：保持切れは “失敗” ではなく “改善項目” としてIMPに必ず落とす

### 5-3. SIEM未連携（Trigger-E）
- 決め：短期は Portal/API（A/B）で必要ログだけ抽出し、Evidence Pack を作る
- 中期は REQ-INT（連携）でロードマップ化（90/180日）

### 5-4. 相関キー欠落（Trigger-D）
- 決め：Tier-4（時刻相関のみ）は unknown を基本にし、無理に yes に倒さない
- 決め：相関キーを増やす “設計改善” は REQ-COR/IMP に落とす

### 5-5. 秘密情報・第三者情報（PII/契約制約）
- 決め：マスキング方針（後述）で “渡せる形” に整形し、作業を止めない
- 決め：提供不可の場合は REQ を rejected にし、DFIR章は unknown/no として根拠付きで報告する

---

## 6. PII/法務/監査の最小ルール（固定）
### 6-1. データ最小化（Data Minimization）
- “必要なフィールドだけ” を提出（common schema + 相関キーを中心に）
- content/body（メール本文、ファイル内容、機密本文）は原則不要（必要なら別合意）

### 6-2. マスキング（Redaction Policy）
- 原則：識別子は残し、内容は落とす
  - userPrincipalName：ドメイン部分のみ残す/ローカル部をハッシュ化など
  - correlation_id / operation_id：原則残す（相関の生命線）
  - IP：社内規定に従い一部マスク可（ただし相関不能になるなら要相談）

### 6-3. 転送経路（Transfer）
- 顧客指定のセキュア経路（SFTP/専用ストレージ/チケット添付）を使う
- “個人メール送付” は禁止（監査上の事故要因）

### 6-4. アクセス制御（Access）
- Evidence Pack は閲覧権限を最小化（need-to-know）
- 参照ログ（誰がいつ閲覧したか）を残せるなら推奨

### 6-5. 監査・証跡（Auditability）
- 提出した Evidence Pack の一覧（E-###）を Evidence Index に固定し、改変疑義を防ぐ
- 可能ならハッシュ（checksum）で raw の同一性を担保（任意・運用が許す範囲）

---

## 7. コミュニケーション（合意形成）ルール（固定）
### 7-1. “口頭合意” を禁止する範囲
- REQ accepted の期限・担当・提出形式
- 提供不可（rejected）の理由と代替案
- マスキング方針（PII取り扱い）

→ これらは必ずトラッカーに記録（comms_log）する。

### 7-2. 進捗報告の粒度（固定）
- 週次（または報告会まで短期なら隔日）
  - P0 REQ の status と次アクション
  - Evidence ready 数
  - unknown_count の推移（KPI）

---

## 8. “最小運用セット”（現場に導入する最小）
最低限これだけ回せば、DFIR章は崩れない。

- BASICS08-01（結論テンプレ）
- BASICS08-03（Evidence Pack）
- BASICS08-07（トラッカー）
- Evidence Workshop（Hop2/5/7 を最優先で1回）

---

## 9. 次のファイル（BASICS08-09）への接続
次は、運用モデルを補強するために、Hop別の “観測点マップ（Network/Host/Identity/Cloud）” を作る。
これにより、unknown/no の理由をアーキとして説明し、改善の説得力を上げる。

- 次：keda-lab/08_blue-dfir/09_observability-map-by-hop.md
  - Hop×観測点（Network/Host/Identity/Cloud）
  - 主要ログソース（Primary/Secondary）
  - 相関キー（Tier-1〜4）
  - 観測穴（unknown/no）になりやすい設計パターン
<<<END>>>
