# PTES Technical Guidelines — 目次 / 章構成（翻訳準備）

以下は、PTES（Penetration Testing Execution Standard）Technical Guidelines の公式構成に基づく章立ての日本語化サマリです。  
この目次を基準に、各章を個別 Markdown ファイルとして翻訳・整備していきます。

---

## 1. はじめに（Introduction）
- ガイドラインの目的  
- PTES の概要と適用範囲  
- 他標準（OSSTMM, NIST SP 800-115, ISO 27001 等）との関係  
- 用語定義と前提条件  

---

## 2. 要件および利用環境（Pre-engagement Requirements）
- テスト前の合意事項（スコープ・契約・倫理）  
- 実施環境の要件（ネットワーク、ハードウェア、仮想化環境など）  
- 使用ツール・プラットフォームの推奨構成  
- 安全な試験実施に関する注意  

---

## 3. 情報収集（Intelligence Gathering）
- 目的と手法の概要  
- OSINT（公開情報収集）  
- 組織・ドメイン・ネットワーク情報の収集  
- 物理的環境や人の要素に関する情報収集  
- 使用ツール・リソース例  

---

## 4. フットプリンティングと列挙（Footprinting and Enumeration）
- ネットワークレベルのスキャン  
- DNS, WHOIS, BGP, ゾーン転送  
- ポートスキャン、サービス検出、バナー解析  
- Web / アプリケーションレベルの列挙  
- 認証・アクセス制御・デバイス情報の特定  

---

## 5. 脆弱性分析（Vulnerability Analysis）
- 自動スキャナと手動分析の併用  
- ネットワーク脆弱性スキャン（Nessus, OpenVAS など）  
- Webアプリケーション脆弱性（SQLi, XSS, CSRF など）  
- 結果の検証と偽陽性の除去  
- 影響度とリスクの評価基準  

---

## 6. エクスプロイト（Exploitation）
- 初期侵入の手法と戦略  
- 権限昇格（Privilege Escalation）  
- クライアントサイド攻撃（ブラウザ・メール経由など）  
- バイパス技術（ASLR, DEP, WAF 回避 等）  
- 安全な実施のための留意点  

---

## 7. ポストエクスプロイト（Post-Exploitation）
- 内部ネットワークでの情報収集  
- 横展開（Pivoting / Lateral Movement）  
- 権限維持（Persistence）  
- 証拠収集と影響範囲の確認  
- 終了時の環境復旧・後処理  

---

## 8. レポーティング（Reporting）
- 技術的報告書と経営層向け報告書の構成  
- リスク評価と推奨対策  
- 発見事項の優先順位付け  
- 修正確認（リテスト）および文書管理  

---

## 9. 付録（Appendices）
- 推奨ツールリスト  
- チェックリスト／テンプレート例  
- 関連標準・参考文献  

---