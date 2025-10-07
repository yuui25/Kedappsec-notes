# Webペネトレーションテスト実施フロー（統合版）

> **目的**：ASVS / WSTG / ATT&CK / PortSwigger Web Security Academy / PayloadsAllTheThings / HackTricks を**一枚の実施フロー**に統合。以降の手順・証跡・報告の“型”として利用する。  
> **適用範囲**：本書は **Kedappsec-notes** 専用（他プロジェクトの運用方針には適用しない）。

---

## 0. 立ち位置と役割分担
- **ASVS**＝検証**要件/深度（L1/L2/L3）**を決める。受入基準（DoD）はASVS要件IDで明記。  
- **WSTG**＝検証**観点/手順**の標準。計画～実施～報告を章節でマッピング。  
- **ATT&CK**＝**攻撃連鎖の説明**に用いる戦術/技術ID。検知/ログも併記。  
- **WSA（PortSwigger）**＝Burp主体の**手技訓練**とラボ→PoC化。  
- **PayloadsAllTheThings**＝**入力ペイロード集**（低リスク→高リスクの段階実施）。  
- **HackTricks**＝**入口拡張/バイパス**の発想源（一次情報で裏取り前提）。

---

## 1. 全体フロー（6フェーズ）
```
Phase 0  Pre-engagement（合意）          →  目的/範囲/禁止/証跡/連絡体制/ASVSレベルを確定
Phase 1  Plan（設計）                   →  ASVS要件ID→WSTG観点へマッピング、証跡テンプレ用意
Phase 2  Find Entry（入口拡張）         →  HackTricksで当たり付け→PATTでペイロード準備
Phase 3  Run（検証）                    →  Burp（Proxy→Repeater→Intruder）、低→高リスク段階
Phase 4  Chain（連鎖の説明）            →  ATT&CKで戦術/技術ID化、データソース（ログ）を付記
Phase 5  Report & Retest（報告/再試験） →  エビデンス整備、DoD判定、修正後の再検証
```
※ 非破壊原則・時間帯/レート・緊急停止は常に遵守。

---

## 2. Phase 0 — Pre-engagement（合意）
**目的**：**何をどこまで**実施して良いかを**書面で固定**し、DoDと安全装置を合意。  
**最低項目**：
- 範囲（URL/機能/API/環境）・時間帯・禁止（DoS/大量送信/破壊）・レート上限  
- アカウント/ロール/MFA方針・テストデータの扱い  
- 証跡（Req/Res原文・スクショ・アプリログ・相関ID・TZ）と機微データ管理  
- **ASVSレベル（例：L2）**と**DoD**（採用要件IDの達成）

---

## 3. Phase 1 — Plan（設計）
1) **ASVS要件IDリスト化**：対象スコープに対応する要件IDを抽出（認証/認可/セッション/入力検証/暗号/ログ等）。  
2) **WSTG観点選定**：4章カテゴリから**適用/除外**を明記し、各観点に**試行と成功判定**を定義。  
3) **証跡テンプレ**：試行ごとに**ASVS/WSTG/ATT&CK**の参照IDと**ログ期待/実測**を記録するフォームを準備。  
> ヒント：WSTG「Testing Framework」を骨子にし、`観点→試行→期待ログ→成功判定→影響`の列を固定する。

---

## 4. Phase 2 — Find Entry（入口拡張）
- **HackTricks**で**403/401 bypass／Host Header／CORS／HPP／Request Smuggling**等の“当たり”を列挙。  
- 列挙に対し**WSTG該当節**を紐付け、**PayloadsAllTheThings**で**低→高リスク**の順にペイロードを用意。

**ミニ例（HPP→ACL回避）**  
`?role=user&role=admin` / 配列化 / 位置・順序・符号化差分→**認可影響**を観測（`閲覧→更新→削除`の最小PoC）。

---

## 5. Phase 3 — Run（検証）
- **Burpの型**：Proxy→Repeater→Intruder（Sniper/Cluster Bomb）。**差分観測**と**レート制御**を徹底。  
- **安全運転**：本番は**最小限**・ステージング優先。破壊/可用性影響は**個別許可**がある場合のみ。  

**代表シナリオ（抜粋）**  
- **認証/セッション**：ロックアウト→MFA→セッション固定/再利用→Cookie奪取成立で記録（ATT&CK T1539）。  
- **認可/IDOR**：`閲覧→更新→削除`の**最小PoC**で影響拡大を示す。  
- **配置/運用**：HTTP Methods（`OPTIONS/PUT/DELETE`）の誤許可から横展開。  
- **API/SSRF**：外部URL入力→内部面/メタデータ到達の兆候を段階検証（Collaborator等でBlind検知）。

---

## 6. Phase 4 — Chain（連鎖の説明）
成立箇所を**ATT&CK**の戦術/技術IDで**経路化**し、**データソース（例：DS0015 Application Log）**に期待ログ/実観測を紐付ける。  
例：`TA0043 Recon → TA0001 T1190（公開アプリ悪用） → T1539（セッションCookie窃取） → T1078（有効アカウント再利用）`。

---

## 7. Phase 5 — Report & Retest（報告/再試験）
**成果物（最小セット）**：
1) **エグゼクティブサマリ**（到達境界：未認証/認証後／水平/垂直／データ影響）  
2) **攻撃連鎖図**（ATT&CK ID、ログ/検知）  
3) **Finding 単票**（タイトル/影響/前提/再現/PoC/証跡/参照ID/推奨）  
4) **DoD判定表**（ASVS採用レベルの**必須要件**の達成/未達）  
5) **再試験方針**（修正差分の再現と副作用確認）

---

## 8. すぐ使えるテンプレ
### 8.1 Finding 単票（コピペ用）
```
[タイトル] 例：IDORで他ユーザ注文の閲覧/更新に到達
[影響] PII/注文データの閲覧/更新/削除（件数/範囲）
[前提] 役割/MFA/初期状態
[再現] 手順番号＋Req/Res差分（最小PoC）
[PoC] Burp Repeater差分 or cURL（非破壊）
[検知] 期待ログ（ATT&CK Data Source）と実観測
[参照] ASVS v5: <要件ID> / WSTG: <章節> / ATT&CK: T####(.###)
[推奨] 暫定（即日）＋恒久（設計/実装/運用）
```

### 8.2 DoD（Definition of Done）雛形
```
準拠標準：OWASP ASVS v5.0.0
検証レベル：L2（合意によりL1/L3可）
スコープ：/auth, /admin, /api/*（ロール：user/admin）
判定：スコープに属するASVS必須要件IDがすべて「満たす」
未達：修正後に再検証（PoC再実行を含む）
証跡：Req/Res・スクショ・アプリログ・Burpプロジェクト（相関ID/時刻）
```

### 8.3 実施前/後チェックリスト
- **前**：合意（目的/範囲/禁止/時間帯/窓口/MFA/証跡）／ASVSレベルと要件ID／WSTG観点表／安全（非破壊・レート）  
- **後**：Findingに参照ID付与／ATT&CKマップ更新／DoD判定と再試験方針

---

## 9. 参考（一次情報）
1. **OWASP ASVS v5.0.0（PDF）**  
   https://github.com/OWASP/ASVS/releases/download/v5.0.0/OWASP_Application_Security_Verification_Standard_5.0.0-en.pdf
2. **OWASP WSTG（Latest）**  
   https://owasp.org/www-project-web-security-testing-guide/latest/
3. **MITRE ATT&CK（Enterprise）**  
   https://attack.mitre.org/
4. **PortSwigger Web Security Academy**  
   https://portswigger.net/web-security
5. **PayloadsAllTheThings（GitHub）**  
   https://github.com/swisskyrepo/PayloadsAllTheThings
6. **HackTricks（Methodology & Web）**  
   https://book.hacktricks.xyz/
