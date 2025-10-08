# ASVS L1 / L2 / L3 比較と Webペネトレーション実務ガイド（v5.0 準拠）
対象：ASVS の検証レベル差分を **Webペネトレーション（非破壊・ステージング）** に落とし込んだ実務指針。  
使い方：調達/見積り時のレベル選定、試験計画、証跡・報告の期待水準のすり合わせに用いる。

---

## 0. 要点サマリ
- **L1：初期防御の確認** — ブラックボックス中心で外形・基本安全性のスクリーニング。短時間で「まず外せない」箇所の是正（HSTS/CORS/Content-Type、CSRF基礎、Cookie Secure 等）。
- **L2：実運用レベル** — 大半のアプリ向け。CSP/`nosniff`/Referrer-Policy、`HttpOnly`/`SameSite`、認証・認可・セッション・API/GraphQL/JWT/OAuth まで**現実的な深度**で検証。
- **L3：高保証** — 金融/医療/公共等。L1/L2前提に、**CSPの厳格運用**、Step-up/MFA、OAuthでの**sender-constrained RT（DPoP/mTLS）**、JWT受入の厳格化などを**漏れなく厳密に**確認（グレーボックス）。

---

## 1. レベル別 比較表（実務観点）
| 観点 | L1 | L2 | L3 |
|---|---|---|---|
| 想定用途 | 初期スクリーニング/外形確認 | 本番アプリの標準検証 | 高リスク/高価値システムの高保証 |
| 情報前提 | ブラックボックス | ブラックボックス＋最小限の補助情報 | グレーボックス（設計/ロール要点） |
| HTTP/Headers | HSTS, CORS, Content-Type, CSRF基礎 | **CSP**, `nosniff`, Referrer-Policy, `frame-ancestors` | **CSP厳格（nonce/hash, object-src/base-uri）**, 一貫適用 |
| Cookie/Session | `Secure`、トークン失効/再発行の挙動観察 | `HttpOnly`, `SameSite`, 再認証/他端末終了UI | 高リスク操作の再認証、全セッション終了オプション |
| 認証 | 最小長/弱PW拒否/変更フロー/レート抑止の兆候 | IdP署名検証・リプレイ防止、MFA運用（採用時） | SMS/PSTNの不採用確認、強い認証（`acr/amr/auth_time`）の外形確認 |
| アクセス制御 | 機能/データレベルの最小確認 | **BOLA/BFLA**、テナント境界 | 特権操作の再認証・境界の形式的確認 |
| API/GraphQL/WS | WSS利用の確認 | GraphQLの深さ/コスト/DoS対策、WS Origin/トークン | （L2強化を網羅的に） |
| トークン/JWT/OAuth | — | `aud/iss/exp`検証、PKCE/`state`/`nonce` | **sender-constrained RT（DPoP/mTLS）**、用途誤用の排除 |
| 構成/クライアント保護 | `.git`等非公開 | ディレクトリリスティング/TRACE無効、ブラウザキャッシュ抑止 | 同左を**全主要パスで一貫**適用 |

---

## 2. レベル別：「Webペネトレで具体的に何をやるか」
### L1（最低限）
1) **外形**：HSTS / CORS / Content-Type、CSRF（Origin/トークン/メソッド）  
2) **Cookie/セッション**：`Secure`、ログアウト/期限切れ時の無効化、認証直後のトークン再発行の挙動  
3) **認証**：最小長・変更フロー・弱PW拒否・UI/UX観点（マスク/ペースト許可 等）  
4) **入力/インジェクション**：XSS/SQLi/OSコマンド **兆候**、XXE無効化  
5) **ファイル/パス**：サイズ上限・拡張子/内容整合・実行抑止・`../`拒否  
6) **漏えい**：`.git`/バックアップ/URLへの機微情報混入抑止  
7) **API/WS**：`wss://`、基本スキーマ整合

### L2（標準）
1) **ブラウザ保護強化**：**CSP**（`object-src 'none'`; `base-uri 'none'`; インラインは nonce/hash）、`nosniff`/Referrer-Policy/`frame-ancestors`  
2) **セッション**：`HttpOnly`/`SameSite`、認証直後のIDローテーション、他端末セッション終了UI  
3) **認証/IdP**：署名検証・リプレイ防止の外形、回復でMFAバイパス不可  
4) **アクセス制御**：**BOLA/IDOR**、**BFLA**、マルチテナント越境抑止  
5) **API/GraphQL/WS**：HTTP Smuggling基礎、GraphQL深さ/コスト/件数、WSのOrigin検証と専用トークン  
6) **ファイル**：圧縮展開サイズ上限、ダウンロード名制御/サニタイズ  
7) **トークン/JWT/OAuth**：`aud`検証・用途誤用無し、PKCE/`state`/`nonce`、必要最小スコープ  
8) **構成**：ディレクトリリスティング/TRACE無効、機微データのキャッシュ抑止/ブラウザ保存禁止、失敗時の安全動作

### L3（最高保証）
1) **CSPの厳格・一貫運用**（主要画面全て／`unsafe-*`の不要許可なし）  
2) **認証強度**：SMS/電話OTPの不採用、MFA・回復フローの堅牢化（バイパス不可）  
3) **セッション**：高リスク操作の再認証、全セッション終了操作、ログアウト動線の常時提示  
4) **OAuth/OIDC**：**sender-constrained RT（DPoP または mTLS）**の採用確認、`state`/`nonce`/PKCE の強固な束縛、OP/同意管理の安全性  
5) **JWT受入**：ID Tokenの誤用なし、`aud`厳格一致  
6) **公開面の締め付け**：ディレクトリリスティング/TRACE/内部ドキュメント非公開、機微のブラウザ保持禁止（`no-store` 等）  
（L3は**網羅性と誤許可ゼロ**を要求）

---

## 3. 実施順のモデル（例）
- **L1（2–4h）**：外形→Cookie→認証UI→CSRF→XSS/SQLi→ファイル→漏えい→API/WS  
- **L2（1–3日）**：CSP→セッション→認証/認可→API/GraphQL→SSRF/HTTP層→JWT/OAuth→構成/クライアント保護  
- **L3（3–10日）**：前提合意→CSP/クリックジャッキング→認証強度→OAuth/OIDC→JWT→公開面→クライアント保護（**全主要パス採取**）

---

## 4. レベル別 観点×要求差分マトリクス（抜粋）
| ドメイン | L1 | L2 | L3 |
|---|---|---|---|
| HTTP/Headers | HSTS, CORS, Content-Type | + **CSP**, `nosniff`, Referrer-Policy, `frame-ancestors` | + **CSP厳格化（nonce/hash, object/base）** |
| Cookie/Session | `Secure`, 失効/再発行挙動 | + `HttpOnly`, `SameSite`, 再認証/他端末終了 | + 高リスク操作再認証、全セッション終了 |
| 認証 | 最小長/弱PW拒否/変更/レート兆候 | 署名検証/リプレイ防止、MFA運用 | SMS不可、強い認証（外形） |
| 認可 | 機能/データ最小限 | **BOLA/BFLA**/テナント | 特権操作の再認証 |
| API/GraphQL/WS | WSS | Smuggling基礎/GraphQL深さ/Origin検証/専用トークン | 網羅適用 |
| ファイル | サイズ/整合/実行抑止/`../` | 展開サイズ/Disposition/名称サニタイズ | 主要パス網羅の安全性 |
| JWT/OAuth/OIDC | — | `aud`/用途/PKCE/`state`/`nonce` | **DPoP/mTLS**・回転/失効設計の外形確認 |
| 構成/クライアント保護 | `.git`等非公開 | Listing/TRACE無効、`no-store`/LocalStorage禁止 | 一貫適用・誤許可ゼロ |

---

## 5. スコーピング補助チェック（抜粋）
- 個人情報/決済/医療/公共データ？→ **L2以上**、規制あり/高リスクなら **L3**。  
- OIDC/OAuth/JWTを運用？→ **L2で必須**（L3では sender-constrained 等を外形確認）。  
- マルチテナント/特権操作が多い？→ **L2で境界検証**、高保証要件なら **L3**。

---

## 6. 成果物（証跡）基準
- **L1**：HTTPログ抜粋・画面・再現手順（代表パス）。  
- **L2**：HTTPログ＋状態遷移図（必要に応じ）、設定値/画面の網羅スクリーンショット。  
- **L3**：上記に加え、**主要パスのヘッダ採取**、OAuth/OIDC ハンドシェイクの**実測証跡**、セッション/再認証の**時系列ログ**。

---

## 7. **WEBペネトレーションテスト範囲から対象外**（代表例）
> 共通理由：**内部実装/設計/運用レビューに依存**し、外部からのWebペネトレのみでは検証妥当性が担保できないため。

- **暗号/鍵管理/PRNG/ハッシュ/KDF**（ASVS V11 系）：内部実装・鍵運用の確認が必要。  
- **自己完結トークンの発行側設計**（9.1.x/9.2.1）：アルゴリズム許可リスト/鍵供給/有効期間は実装確認が必要。  
- **通信のmTLS/内部TLS強制**（12.1.3/12.3.3）：内部ネットワーク設計の確認が必要。  
- **ファイル許可拡張/AV運用**（5.1.1/5.4.3）：運用/文書確認中心。  
- **ログ/エラー/イベント定義**（V16の多く）：設計/運用・形式のレビューが中心。

---

## 8. ASVS要件ID マッピング（抜粋・v5.0.0準拠）
- **HTTP/Headers**：L1→3.4.1/3.4.2/4.1.1、L2→3.4.3/3.4.4/3.4.5/3.4.6、L3→3.4.3（厳格・網羅適用）。  
- **Cookie/Session**：L1→3.3.1/7.2.4/7.4.1、L2→3.3.2/3.3.4/7.5.1/7.5.2/7.4.3/7.4.4、L3→7.4.3/7.5.2/7.4.4。  
- **認証**：L1→6.2.1〜6.2.8/6.2.4/6.1.1、L2→6.4.3/6.8.2/6.8.3、L3→6.6.1/6.4.3/6.8.4（外形）。  
- **API/GraphQL/WS**：L1→4.4.1、L2→4.1.2/4.1.3/4.2.1/4.3.1/4.3.2/4.4.2〜4.4.4、L3→（L2項目の網羅適用）。  
- **ファイル**：L1→5.2.1/5.2.2/5.3.1/5.3.2、L2→5.2.3/5.4.1/5.4.2、L3→（L2項目の抜けなし確認）。  
- **JWT/OAuth/OIDC**：L2→9.2.2/9.2.3/10.1.1/10.1.2/10.2.1/10.2.2/10.3.1/10.3.2/10.3.4/10.4.6〜10.4.11/10.5.x/10.6.x/10.7.x、L3→**10.4.5（DPoP/mTLS）**。

---

## 参考（一次情報）
- OWASP ASVS（v5.0.0 本体/ダウンロード）: https://owasp.org/www-project-application-security-verification-standard/  
- OWASP Developer Guide（ASVSの使い方/レベル定義）: https://devguide.owasp.org/en/03-requirements/05-asvs/  
- OWASP WSTG（詳細手順書）: https://owasp.org/www-project-web-security-testing-guide/latest/  

---

**Trace**  
- created: 2025-10-09 JST  
- source: *asvs-l1-web-pentest-guide.md*, *asvs-l2-web-pentest-guide.md*, *asvs-l3-web-pentest-guide.md*（いずれも v5.0.0 準拠）  
- maintainer: kedappsec-notes editors
