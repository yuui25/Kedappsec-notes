# 17_rdap_whois_組織境界（登録者_委託_関連会社）.md

## ガイドライン対応（ASVS / WSTG / PTES / MITRE ATT&CK）
- ASVS：V1（アーキテクチャ/境界）を評価する前提として、ドメイン登録情報（RDAP/WHOIS）が示す「登録者/運用者/委託」の可能性を境界情報として固定する。V14（運用/構成）では、登録情報の運用（代理登録/保護/更新）を“運用境界”として扱う。
- WSTG：Information Gathering（組織・資産・責任分界の把握）として、技術観測（DNS/TLS/HTTP/ASN）だけでは確定しづらい“組織境界”を補強する。
- PTES：Intelligence Gathering（ターゲット理解）で、技術境界（08_asn_bgp等）と組織境界（RDAP）を突合し、スコープ逸脱を防ぐ。
- MITRE ATT&CK：Reconnaissance（組織情報の収集）として位置づける。ただし目的は攻撃のための特定ではなく、診断の範囲確定と責任分界の明確化。

## 目的（この技術で到達する状態）
- RDAP/WHOISから、**登録主体（registrant）/レジストラ/ネームサーバ運用** の情報を取得し、資産境界（誰の資産か）を yes/no/unknown で整理できる。
- 代理登録・プライバシー保護・グループ会社・委託運用の可能性を前提に、**断定しない境界モデル**（推定の強弱）でスコープ判断を支援できる。
- 06_subdomain/08_asn_bgp/10_ctlog/11_cohosting と結合し、「技術的に近いが組織的に別」の混入を早期に見抜き、過剰な深掘りを避けられる。

## 前提（対象・範囲・想定）
- 対象：
  - ルートドメイン（example.com）と主要サブドメイン（通常は登録はルート単位）
  - 関連ドメイン（ブランド/国別/子会社）候補（ただし“候補”として扱う）
- 想定される落とし穴：
  - プライバシー保護（Redacted/Proxy）で登録者が見えない（unknownが増える）
  - 大企業/グループ会社で登録名義が統一されない（別組織に見えるが実際は同一、または逆）
  - レジストラやDNSホスティングが委託先（第三者境界）で、登録者＝運用者ではない
- できること/やらないこと：
  - できる：公開登録情報から境界の仮説を立て、他観測（DNS/ASN/TLS/HTTP）と突合して強度を上げる
  - やらない：登録者情報の個人特定・過剰な掘り下げ（本プロジェクトは“境界の確定”が目的）

## 観測ポイント（何を見ているか：プロトコル/データ/境界）
### 1) WHOISよりRDAPを優先する理由（観測の安定性）
- RDAPはJSON構造で機械処理しやすく、レートや表記揺れを抑えやすい。
- WHOISはテキストで表記揺れが大きく、レジストリごとに項目が異なる。補助として扱う。

### 2) 取得したいフィールド（境界モデル化に必要最小限）
- 登録情報の“運用者側”：
  - registrar（レジストラ）
  - nameservers（NS。DNS委託先の候補）
- 組織境界の“匂い”：
  - registrant / admin / tech（ただしredactedが多い）
  - org name の表記（一致/不一致）
  - country / region（国別事業・委託の匂い）
- 時系列：
  - registration/updated/expiration（更新・移行・委託変更の痕跡になり得る）

### 3) “断定しない”ための強度スコア（強/中/弱）
RDAP単体では断定せず、他観測と束ねて強度を上げる。
- 強（組織境界が強く示唆）：
  - 複数ドメインでregistrar/NS/登録名義（見える範囲）が一貫し、かつ 10_ctlog（Issuer/証明書の束）も近い
- 中（委託/グループ会社の可能性）：
  - registrarは同じだがNSが違う、またはその逆
  - 登録者は見えないが、NSが特定企業/事業部のパターンに寄る
- 弱（判断保留）：
  - ほぼ全てredacted、または情報が少ない
  - 大手レジストラ＋大手DNSで差が出にくい

### 4) 境界（資産/信頼/権限）に落とす
- 資産境界：
  - “登録名義”ではなく「診断対象の責任分界」を作るために使う
  - 近いドメインでも、登録/委託が別なら対象外混入の可能性として扱う
- 信頼境界：
  - DNS運用が第三者（NS）なら、05_cloud/12_waf_cdnと同様に“第三者境界”として整理する
  - IdP/SSOのドメイン（04_saas/01_idp）など、外部連携ドメインの位置づけを補強できる
- 権限境界：
  - 直接の権限判定には使わないが、ID基盤/管理系の“運用主体”が別だと、修正主体が変わる（報告の分解に効く）

## 結果の意味（その出力が示す状態：何が言える/言えない）
- 言えること（状態）：
  - registrar/NSなどから、運用委託の可能性・組織境界の仮説を立てられる
  - 近接ドメインの“同一組織らしさ”を強/中/弱で整理できる
  - 技術観測（ASN/TLS/HTTP/CT）と束ねることで、スコープ逸脱リスクを下げられる
- 言えないこと（断定しない）：
  - 登録者＝運用者＝資産所有者の断定
  - redacted環境での個人/組織の特定（unknownとして扱う）
  - “同一組織だから安全/危険”の断定

## 攻撃者視点での利用（意思決定：優先度・攻め筋・次の仮説）
- 優先度が上がる状態（診断の意味）：
  - 入口（06/10/15/16）で見えた“怪しい近接ドメイン”が、RDAPで別組織っぽい → 深掘りを止める（スコープ保護）
  - 逆に、技術的にも組織的にも近い束が見える → “同一資産群”として優先度を上げ、Web/NW検証を効率化
- 次の仮説：
  - 仮説A：情報がredactedで判断できない
    - 次：08_asn_bgp/10_ctlog/11_cohosting の束で補強し、unknownを減らす
  - 仮説B：NSが委託先へ寄っている（第三者境界が濃い）
    - 次：05_cloud/12_waf_cdn で運用境界（CDN/WAF/DNS）をまとめて説明できる形にする
  - 仮説C：グループ会社で名義がバラける
    - 次：ドメイン群を“ブランド/地域/事業”でクラスター化し、境界の説明軸を変える

## 次に試すこと（仮説A/Bの分岐と検証）
- 仮説A（redactedで判断不能）：
  - 次の一手：
    - registrar/NSだけでも記録し、他観測（CT/ASN/HTTP）で束を作る
    - unknownを“境界として残す”判断を明示する（無理に同一と断定しない）
  - 到達点：
    - 境界の不確実性を管理でき、スコープ逸脱を防げる

- 仮説B（委託境界が濃い）：
  - 次の一手：
    - NSとCDN/WAFの観測（05_cloud/12_waf_cdn/11_cohosting）を束ね、第三者境界を1枚絵で説明できるようにする
    - 監査・報告では「自社アプリ」「委託DNS」「委託CDN/WAF」を分ける
  - 到達点：
    - 修正主体・責任分界が明確になり、報告の精度が上がる

- 仮説C（名義が分散し、組織境界が揺れる）：
  - 次の一手：
    - 近接ドメインの“共通点”を registrar/NS/CT/HTTP から抽出し、強度（強/中/弱）を付ける
    - 強度が弱いものは “候補止まり” として扱い、入口（FQDN）単位でのみ追う
  - 到達点：
    - “似ている”と“同じ”を混同せずに運用できる

## 手を動かす検証（Labs連動：観測点を明確に）
- 関連 labs：
  - `04_labs/01_local/01_attack-box_作業端末設計.md`（データ保存と差分管理）
- 取得する証跡（目的ベースで最小限）：
  - `rdap_raw.json`（取得元・取得日時付き）
  - `rdap_summary.csv`（domain, registrar, ns, redacted_flag, updated, notes）
  - `org_boundary_notes.md`（強/中/弱と根拠の束）

## コマンド/リクエスト例（例示は最小限・意味の説明が主）
~~~~
# 目的：RDAPからregistrar/NS/更新時刻を取得し、組織境界の材料を作る
# 注：TLDによりRDAPエンドポイントは異なる。まずはIANA RDAP bootstrapを使う運用が現実的。

# (1) IANA RDAP bootstrap（DNS経由）からRDAPサーバを引く（例示）
# 実運用では“RDAPクライアント”やライブラリで自動化することが多い。

# (2) 直接RDAP（例：.com系の一般的な形）
curl -s "https://rdap.verisign.com/com/v1/domain/example.com" > rdap_raw.json

# (3) 必要フィールドだけ抜粋（registrar/NS/更新日など）
python -c "import json; d=json.load(open('rdap_raw.json')); \
ns=[x.get('ldhName') for x in (d.get('nameservers') or []) if isinstance(x,dict)]; \
events={e.get('eventAction'):e.get('eventDate') for e in (d.get('events') or []) if isinstance(e,dict)}; \
reg=(d.get('entities') or []); \
print('nameservers=',ns); print('events=',events); print('entities_count=',len(reg))"
~~~~
- 観測していること：
  - registrar/NS/更新イベントなど、組織境界・委託境界の“匂い”になる要素

## 参考（必要最小限）
- `01_topics/01_asm-osint/01_dns_委譲・境界・解釈.md`
- `01_topics/01_asm-osint/08_asn_bgp_ネットワーク境界（AS_プレフィックス_関連性）.md`
- `01_topics/01_asm-osint/10_ctlog_証明書拡張観測（SAN_ワイルドカード_中間CA）.md`
- `01_topics/01_asm-osint/11_cohosting_同居推定（共有IP_VHost_CDN収束）.md`
- `01_topics/01_asm-osint/05_cloud_露出面（CDN_WAF_Storage等）推定.md`

## リポジトリ内リンク（最大3つまで）
- 関連 topics：`01_topics/01_asm-osint/08_asn_bgp_ネットワーク境界（AS_プレフィックス_関連性）.md`
- 関連 playbooks：`02_playbooks/01_asm_passive-recon_資産境界→優先度付け.md`
- 関連 labs：`04_labs/01_local/01_attack-box_作業端末設計.md`