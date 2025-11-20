# Recon 01〜07 実施ツール一覧 & 所要時間（スキルレベル：中級）

本ドキュメントは、Recon（01〜07）を実施する際の  
**使用ツール** と、**画面規模別の所要時間（中級者向け）** を 1 ファイルに統合したもの。

上級者よりも「調査・判断・照合」に時間がかかることを前提とし、  
現場実務に近い工数で再算出している。

---

# 1. フェーズ別：使用ツール一覧（王道ツールのみ）

---

## 01_Tech_Fingerprinting
**使用ツール**
- Chrome/Firefox DevTools（Network / Application / Security）
- Burp Suite（Proxy）
- curl
- Wappalyzer（補助）

**主内容**
- スタック推定、CSP/CORS 抽出、Token Storage 判定

---

## 02_Attack_Surface_Mapping
**使用ツール**
- Burp Suite（Proxy / Target / Sitemap）
- Chrome DevTools → Network（XHR/JS）
- VEX（ログ補助）

**主内容**
- UI遷移 → API抽出、JS一覧化、Hidden API 発見

---

## 03_JS_API_Mapping（最重要フェーズ）
**使用ツール**
- Chrome DevTools（Sources / Network）
- Burp Suite（Auth header/Token）
- VSCode（JS閲覧）
- source-map explorer（mapがある場合）

**主内容**
- fetch/XHR完全抽出、ID/Role/Tenant構造推論、CRUD/非公開API把握

---

## 04_SaaS_Cloud_Misconfig
**使用ツール**
- Burp Suite（CSP/CORS）
- DevTools Security パネル
- dig / nslookup / host
- curl（/.well-known 等）

**主内容**
- Hosting/CDN 判定、SaaS routing、Storage推定、CORS確認

---

## 05_BAC_Logic_Candidates
**使用ツール**
- Burp Suite（API仕様把握）
- ブラウザ DevTools（UIとJS照合）

**主内容**
- UI vs API 差分、Role/Tenant/Owner 権限境界推論

---

## 06_Findings_Hypothesis
**使用ツール**
- VSCode / Obsidian / Notion（まとめ）
- Kedappsec-notes（md格納）

**主内容**
- 脆弱性仮説化、優先候補抽出

---

## 07_Next_Actions
**使用ツール**
- Burp Suite（Repeater中心）
- VEX（補助）
- DevTools（UI整合確認）

**主内容**
- BAC各種検証、Flow/State/Hidden API 深掘り

---

# 2. 所要時間の目安（中級者向けに再算出）

上級者と比べて「調査・理解・照合」に時間を要することを前提とし、  
**現実的かつ中級診断員として妥当な時間** に引き上げています。

---

# ■ 画面数 10（小規模アプリ）
| フェーズ | 時間（中級） |
|---------|--------------|
| 01 | 30〜45分 |
| 02 | 45〜75分 |
| 03 | 60〜120分 |
| 04 | 30〜45分 |
| 05 | 45〜60分 |
| 06 | 20〜40分 |
| 07 | 30〜45分 |

**合計：4〜6時間**

→ **半日〜1日弱**  
→ 中級者ならこの規模でも Recon が午前中では終わりにくい。

---

# ■ 画面数 50（中規模 SaaS / 業務システム）
| フェーズ | 時間（中級） |
|---------|--------------|
| 01 | 45〜60分 |
| 02 | 90〜150分 |
| 03 | 150〜240分 |
| 04 | 45〜75分 |
| 05 | 90〜120分 |
| 06 | 45〜75分 |
| 07 | 60〜120分 |

**合計：10〜16時間（1.5〜2日）**

→ 上級者の 1日完結より伸び、中級者として現実的。

---

# ■ 画面数 100（大規模 SaaS / 企業向けシステム）
| フェーズ | 時間（中級） |
|---------|--------------|
| 01 | 60〜90分 |
| 02 | 2〜4時間 |
| 03 | 5〜9時間 |
| 04 | 60〜120分 |
| 05 | 2〜3時間 |
| 06 | 1.5〜3時間 |
| 07 | 2〜4時間 |

**合計：2〜3.5日**

→ 特に 03_JS/API Mapping が重く、中級者は時間を要する  
（SPA/React/Next.js の場合は更に +1日もあり得る）。

---

# まとめ（中級者向け現実値）
- 使用ツールは **DevTools + Burp Suite** が主軸  
- 所要時間は上級より **20〜50% 増加**  
- 規模別総時間  
  - **画面10：4〜6時間**  
  - **画面50：10〜16時間（1.5〜2日）**  
  - **画面100：2〜3.5日**  
- 最大工数は 03_JS_API_Mapping  
  → 理解と抽出に時間を要するので妥当な値

---
