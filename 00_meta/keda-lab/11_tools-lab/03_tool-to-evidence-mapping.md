<<<BEGIN>>>
# 11_tools-lab / BASICS11-03 ツール→Evidence→Hop→章 マッピング（最小出力で過収集を防ぐ）

## 1. 本ファイルの役割（本編01〜05への接続）
本ファイルは、11_tools-lab を「実務で回る形」にする中核である。

- ツールを選ぶ基準を「好き嫌い」ではなく  
  **“必要な Evidence を生むか”** に固定する
- 実行した結果がそのまま
  - STEP5-09 Evidence Index
  - STEP5-09 Timeline
  - STEP5-10 Report（章の Snippet）
 へ流れるようにする
- さらに、STEP5-12（シナリオ別×章別必須Evidenceマトリクス）の `M` を  
  **どのツールで埋めるか** を割り当てる

---

## 2. マッピングの読み方（固定）
### 2-1. Evidence 種類（短縮語）
- OSINT / AUTH-LOG / CA-POL / AD-DISC / LM-LOG / PRIV / AADC / CLOUD-AUDIT / API-SAMPLE / DFIR / ROE
- （Web起点追加）HTTP-EVID（HTTPリク/レス＋再現＋影響根拠）

### 2-2. 出力粒度（過収集防止）
- L1：観測（設定/ログ/画面）＝原則これで足りる箇所を明示
- L2：最小サンプル（メタ情報、件数制限、期間制限）＝TLPTは基本ここまで
- L3：実行（合意範囲）＝必要時のみ、Evidence を強くする

### 2-3. “最小出力”の定義
- 取るべきフィールド（必要最小）を先に決める
- 期間/件数/対象を制限する（例：24h、上位20件、特定ユーザーのみ）
- raw は隔離、sanitized は Fact のみ

---

## 3. Hop別：必須Evidence（STEP5-07/12 由来）
- Hop1（Recon）：OSINT
- Hop2（Session）：AUTH-LOG / CA-POL（＋到達範囲）
- Hop3（AD Discovery）：AD-DISC
- Hop4（Material）：PRIV（補助的に“材料の存在”）
- Hop5（Lateral）：LM-LOG / PRIV
- Hop6（Hybrid）：AADC（＋CLOUD-AUDIT で波及根拠）
- Hop7（Cloud）：CLOUD-AUDIT / API-SAMPLE（＋PRIV）

---

## 4. ツール→Evidence→Hop→章 マッピング（推奨セット）

### 4-1. Hop1：Recon（OSINT）

| Evidence | 代表ツール/手段 | 最小出力（過収集防止） | Hop | Report章 |
|---|---|---|---|---|
| OSINT | ブラウザ/検索/証明可能な参照 | 入口URL一覧＋取得日時＋ソース種別 | Hop1 | KC/ES（起点選定） |
| OSINT | DNS/CTログ系（Passive） | “発見した入口”の根拠（スクショ/ログ） | Hop1 | KC |
| ROE | 合意文書/議事録 | 禁止事項/データ最小化方針（1枚） | Hop1 | SC/ES |

※本プロジェクトでは特定ツール名より「出力の形」を優先する。

---

### 4-2. Hop2：社員化/セッション（AUTH-LOG / CA-POL / 到達範囲）

| Evidence | 代表ツール/手段 | 最小出力 | Hop | Report章 |
|---|---|---|---|---|
| AUTH-LOG | IdP/VPN/VDI 管理ログ抽出 | 期間24h、対象ユーザー/テストIP、成功/失敗、要求MFA有無 | Hop2 | KC/DF/ES |
| CA-POL | CAポリシー現在値（画面/Export） | 適用条件、例外、MFA要求、対象アプリ | Hop2 | ES/FD |
| （到達範囲） | 端末側観測（route/dns等） | 内部に見える範囲の要約（ネットワーク名/アプリ） | Hop2 | KC/FD |

---

### 4-3. Hop3：AD/内部ディレクトリ探索（AD-DISC）

| Evidence | 代表ツール/手段 | 最小出力 | Hop | Report章 |
|---|---|---|---|---|
| AD-DISC | ディレクトリ列挙（要約作成） | OU/Group/重要サーバ候補の“要約表”のみ | Hop3 | KC/FD |
| AD-DISC | 資産候補の根拠（出力ログ） | 列挙の出力を raw に保存し、sanitized で要約 | Hop3 | KC |

※“ユーザー全列挙”などの過収集を避け、まず「高価値候補」へ絞る。

---

### 4-4. Hop4：材料（PRIV/接続材料の存在）

| Evidence | 代表ツール/手段 | 最小出力 | Hop | Report章 |
|---|---|---|---|---|
| PRIV | ロール/グループ確認 | “権限根拠”となる1画面/1出力 | Hop4 | FD/ES |
| （材料存在） | 設定/接続情報の観測 | 接続先“存在”のみ（秘密はマスク） | Hop4 | KC |

---

### 4-5. Hop5：横展開（LM-LOG / PRIV）

| Evidence | 代表ツール/手段 | 最小出力 | Hop | Report章 |
|---|---|---|---|---|
| LM-LOG | Windows/EDR ログ（ログオン） | 対象ホスト＋該当時刻±15分＋ログオン種別 | Hop5 | KC/DF |
| PRIV | 権限変化の根拠 | グループ所属/ローカル管理者/ロールの根拠 | Hop5 | FD/ES |
| DFIR | 検知有無の観測 | アラート/相関イベントの有無（根拠ログ） | Hop5 | DF/ES |

---

### 4-6. Hop6：Hybrid（AADC / 波及根拠）

| Evidence | 代表ツール/手段 | 最小出力 | Hop | Report章 |
|---|---|---|---|---|
| AADC | Connect/同期の構成証跡 | “これが同期点”を示す設定/役割の証拠 | Hop6 | KC/FD |
| CLOUD-AUDIT | 監査ログ（関連操作） | 同期/権限変更に関わるイベントの抜粋 | Hop6 | KC/DF |

---

### 4-7. Hop7：Cloud（CLOUD-AUDIT / API-SAMPLE / PRIV）

| Evidence | 代表ツール/手段 | 最小出力 | Hop | Report章 |
|---|---|---|---|---|
| CLOUD-AUDIT | Entra Audit/Sign-in | 該当時刻±1h、対象アプリ/ユーザー、操作種別 | Hop7 | KC/DF/ES |
| API-SAMPLE | Graph/SaaS API 最小サンプル | メタ情報のみ、件数上限、期間上限 | Hop7 | KC/ES |
| PRIV | ロール/Consent | ロール割当/同意/権限スコープの根拠 | Hop7 | FD/ES |

---

## 5. シナリオ別（A〜E）Must Evidence を“どれで埋めるか”（割当表：最小）

### 5-1. シナリオA（フィッシング起点）Must
- AUTH-LOG：IdP/VDI/VPN 管理ログ抽出
- CLOUD-AUDIT：Entra Audit/Sign-in 抽出
- PRIV：ロール/権限根拠（グループ/Entraロール）
- AADC：同期点の構成証跡
- API-SAMPLE：メタ情報サンプル（L2）
- DFIR：検知有無の根拠ログ
- ROE：データ最小化と非実行範囲

### 5-2. シナリオE（クラウド起点）Must
- CA-POL：CA/例外の現在値
- CLOUD-AUDIT：同意/権限変更/管理操作の監査ログ
- API-SAMPLE：最小サンプル（L2）
- PRIV：Role/Consent の根拠
- （必要なら）AADC：オンプレ波及の根拠

---

## 6. “最小出力”の標準（Evidence 種類ごと）
### 6-1. AUTH-LOG（最小）
- 期間：24h（もしくは該当時間±1h）
- フィールド：時刻、結果、ユーザー、アプリ、MFA要求、条件付きアクセス結果、IP（必要ならマスク）

### 6-2. CLOUD-AUDIT（最小）
- 期間：該当時間±1h（または24h）
- フィールド：操作種別、実行者、対象、結果、相関ID（ある場合）

### 6-3. API-SAMPLE（最小）
- 件数：上位20件など上限
- 取得：メタ情報のみ（本文/添付は取らない）
- 出力：HTTP結果＋取得項目一覧（sanitized は Fact のみ）

### 6-4. LM-LOG（最小）
- 対象ホスト：該当ホストのみ
- 期間：±15分（必要なら±1h）
- フィールド：ログオン種別、アカウント、ソース、結果

---

## 7. 次のファイル（BASICS11-04）への接続
次は、マッピングを実運用に耐えるように「時刻統一」と「ハッシュ運用」を固定する。

- 次：keda-lab/11_tools-lab/04_time-sync-and-hash-policy.md
  - JST/ISO の統一ルール
  - 証跡の改ざん検知（hash-manifest）
  - raw/sanitized の整合（同一Evidence追跡）
  - タイムライン相関（相関キーの扱い）

<<<END>>>
