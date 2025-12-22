## ガイドライン対応（ASVS / WSTG / PTES / MITRE ATT&CK：毎回記載）
- ASVS
  - この技術で満たす/破れる点：
    - 破れる：ディレクトリ（AD）と端末設定（GPO）が“設計上の権限境界”になっている環境では、Webアプリの脆弱性が無くても、GPOの変更権限（DACL/委任）が突破口となり、横展開・永続化・防御回避が成立する。
    - 満たす：GPOの作成/編集/リンク（適用範囲変更）を最小権限で分離し、Tier分離・変更監査（AD変更＋SYSVOL変更）・承認フローで統制する。
- WSTG
  - 該当テスト観点への接続：
    - WebアプリがIWA（Kerberos/Negotiate）やAD連携（LDAP/SMB/WinRM/RDP）に依存する場合、GPOは「端末/サーバの防御設定」「認証ポリシー」「ログ設定」を一括で上書きし得るため、WSTGの認証/セッション/認可評価の“前提条件（環境依存）”として扱う。
- PTES
  - 位置づけ：Post-Exploitation / Lateral Movement / Persistence の“成立条件”を、Enumeration（AD/ACL/GPOリンク）で確定し、影響評価（適用範囲）→是正（権限分離）→検知（変更監査）まで閉じる。
  - 前後の繋がり（1行）：
    - `15_acl_abuse` で見つけた「OU/GPOへの書き込み権限」を、`05_scanning` の到達性と結合し、GPOが“どの資産に適用されるか”を根拠付きで優先度付けする。
- MITRE ATT&CK
  - 戦術：Persistence / Privilege Escalation / Lateral Movement / Defense Evasion
  - 位置づけ：GPOは“正規の構成管理”であり、権限が崩れると「正規機能の濫用」で広範囲の永続化が成立する。

---

# 16_gpo_永続化と権限境界

## タイトル
GPO（Group Policy）：GPC（AD）＋GPT（SYSVOL）の二重構造と、Link権限/編集権限/配布経路を分離して捉え、永続化の成立条件を“権限グラフ”として確定する

---

## 目的（この技術で到達する状態）
- 次を“状態”として断言できるようにする（曖昧な一般論で終えない）。
  1) GPOが **どこに保存され（AD側=GPC / SYSVOL側=GPT）**、クライアントが **どう取得して適用するか** を説明できる
  2) 永続化に繋がるGPO操作を **3つの権限（作成/編集/リンク）** に分解し、どれが欠けると成立しないかを言える
  3) 「この主体がこのOU/Domainに対してGPLinkを書ける」「この主体がこのGPOを編集できる」を、ACL（DACL）と属性（gPLink等）で根拠化できる
  4) 影響（適用範囲）を、OU階層・リンク順序・Enforced/Block・Security Filtering・WMI Filtering・Loopbackで説明できる
  5) 報告で、是正（権限分離・Tier設計・変更監査）と検知（AD変更＋SYSVOL変更＋クライアント適用ログ）まで提示できる

---

## 前提（用語と構造：ここを混ぜると薄くなる）
### 1) GPOは「ADの参照情報（GPC）」と「SYSVOLの実体（GPT）」の2つで成立する
- GPC（Group Policy Container）
  - AD内の `groupPolicyContainer` オブジェクト（参照情報）
  - 代表属性：`gPCFileSysPath`（GPTの場所を指す）
- GPT（Group Policy Template）
  - SYSVOL配下にあるファイル/フォルダ群（設定・スクリプト・Preferences等）
- 重要：クライアントは「ADでGPCを見て（どれが適用対象か）→ gPCFileSysPathを得て → そのパスのGPTを読みに行く」という二段で動く。

### 2) 適用の境界（Scope）は “どこにリンクされ、どの条件で絞られるか”
- リンク単位：Site / Domain / OU
- 適用順序（概念）：Local → Site → Domain → OU（OUは階層＋リンク順序）
- 絞り込み：
  - Security Filtering（GPOの適用対象セキュリティプリンシパル）
  - WMI Filtering（端末条件での適用分岐）
  - Loopback（ユーザー設定の適用を「ログオン先コンピュータ基準」に変える：キオスク/VDI等）

### 3) 永続化の成立条件は「作成/編集/リンク」の組み合わせで決まる（ここを分ける）
- 作成（Create）：新しいGPOを作れる
- 編集（Edit）：既存GPOの設定（GPT/GPC）を変更できる
- リンク（Link）：OU/Domain/SiteにGPOを関連付けて適用範囲を変えられる（=影響を拡げられる）
- 重要：編集権限だけでは影響範囲が小さい場合がある。リンク権限だけでも「既存の強いGPO」を繋ぎ替える/優先順序変更で影響が出ることがある。

---

## 観測ポイント（何を見ているか：プロトコル/データ/境界）
### 観測対象（一次データ）
- AD（LDAP）
  - GPO：`CN=Policies,CN=System,<DomainDN>` 配下の `groupPolicyContainer`
    - 取得したい属性：`displayName`, `name`（GUID）, `gPCFileSysPath`, `versionNumber`, `whenChanged`, `nTSecurityDescriptor`
  - OU/Domain：`gPLink`, `gPOptions`（リンク設定、ブロック等）
    - 取得したい属性：OU DN, `gPLink` の中身（リンクされたGPO GUID群＋linkOptions）
- SYSVOL（SMB）
  - `\\<domain>\SYSVOL\<domain>\Policies\{GUID}\` 配下のファイル
    - GPTの入口：`GPT.INI`（バージョンなど）
    - 永続化に繋がりやすい場所（例）：Scripts / Preferences（ScheduledTasks/Services/Registry/Groups等のXML）

### 境界（Boundary）を3種類で固定する
- 資産境界：影響を受ける端末/サーバはどれか（Tier0/DC/管理サーバ/基幹サーバ/一般端末）
- 信頼境界：GPO編集やリンクを許されている主体は誰か（運用委任グループが“広すぎないか”）
- 権限境界：DACL（WriteDACL/GenericAll/WriteProperty）と、OU上の `gPLink` 変更権限がどこにあるか

---

## 実施方法（最高に具体：棚卸し→成立条件→影響→是正→検知）
> 原則：本番で“攻撃的変更”をしない。検証が必要なら、検証OU/検証GPOで「無害な設定」を使い、必ず巻き戻す。

---

### Step 0：入力を固定（再現性）
- 必須入力
  - DomainDN（例：DC=corp,DC=local）
  - 代表DC（LDAP/SMBの接続先）
  - 自分の観測点（内部端末 / VPN / ピボット越し）
- 任意入力（あると強い）
  - RSAT（GroupPolicy PowerShell）使用可否
  - BloodHound等のグラフ化可否（15_acl_abuseと連携）

---

### Step 1：GPOの全量棚卸し（GPC一覧を作る）
#### 1-A（推奨）：Windows/RSATで一覧＋レポートを作る（読み取り中心）
- 目的：GPOのGUID・表示名・変更時刻・リンク先を短時間で揃える
~~~~
# 例：GPO一覧（GUIDと表示名）
Get-GPO -All | Select-Object DisplayName, Id, Owner, CreationTime, ModificationTime

# 例：特定GPOの内容をXMLで出力（“何が設定されているか”を後で機械的に検索できる形にする）
Get-GPOReport -Guid <GPO_GUID> -ReportType Xml -Path .\gpo_<GPO_GUID>.xml
~~~~
- 判断（次の一手）
  - “重要GPO”候補（Default Domain Policy / Default Domain Controllers Policy / 基幹サーバOUにリンク）を先に抽出して深掘りへ

#### 1-B：Linux/LDAPでGPCを棚卸し（RSAT不可の現場用）
- 目的：GPO（groupPolicyContainer）の事実（GUID・gPCFileSysPath等）をLDAPで確定
~~~~
# base: CN=Policies,CN=System,<DomainDN>
ldapsearch -x -H ldap://<DC_IP> -b "CN=Policies,CN=System,<DomainDN>" -s one \
  "(objectClass=groupPolicyContainer)" \
  displayName name gPCFileSysPath versionNumber whenChanged
~~~~
- 判断（次の一手）
  - `gPCFileSysPath` が想定のSYSVOL（\\domain\SYSVOL\domain\Policies\{GUID}）から逸脱していないかを確認（逸脱は“参照先の境界”が崩れている可能性）

---

### Step 2：リンク（適用範囲）棚卸し（OU/DomainのgPLinkを解析する）
> 「どのGPOが危険か」は、内容だけでなく“どこに効くか”で決まる。

#### 2-A：OUに紐づくGPOリンクをLDAPで列挙
~~~~
# gPLinkを持つOUを列挙（=どこにGPOがリンクされているか）
ldapsearch -x -H ldap://<DC_IP> -b "<DomainDN>" -s sub \
  "(&(objectClass=organizationalUnit)(gPLink=*))" \
  distinguishedName gPLink gPOptions
~~~~

#### 2-B：gPLink文字列の読み方（最低限）
- 典型形式（概念）：
  - `[LDAP://cn={GPO-GUID},cn=policies,cn=system,DC=...;linkOptions]` が連結して入る
- linkOptions（例）
  - Enabled/Disabled、Enforced（強制）等がビットで表現される
- 判断（次の一手）
  - Domain直下/上位OUに強制（Enforced）があるGPOは、影響が広いので優先度を上げる
  - 逆に“下位OUのBlock Inheritance”で効かない経路があるため、必ずOU階層と一緒に評価する

#### 2-C：RSATで継承・リンク状態を可視化（可能なら）
~~~~
# 例：OU単位の継承状況（どのGPOが上から降りてくるか）
Get-GPInheritance -Target "OU=<OUName>,<DomainDN>"
~~~~
- 判断（次の一手）
  - 影響範囲（適用対象コンピュータ/ユーザー）を“OU単位”で確定し、次のStep 3で「編集/リンク権限の所在」と結合する

---

### Step 3：成立条件（権限境界）を分解して確認する
> GPOの悪用は「編集できる」だけでは足りないことがある。必ず“どの権限がどの結果を生むか”を分ける。

#### 3-1 編集（Edit）権限：そのGPO自体を書き換えられるか
- 何を確認するか（状態）
  - 対象GPO（groupPolicyContainer）のDACLで、主体Sが
    - GenericAll / WriteDACL / WriteOwner / GenericWrite 等の“状態遷移権限”を持つか
  - 併せてSYSVOL側（GPT）への書き込みが実質可能か（運用上はGPO編集権限に含まれることが多いが、逸脱があると境界が崩れる）
- 実施（読み取り）
  - `15_acl_abuse` の手順で、GPOオブジェクトのDACLを抽出し、危険権限だけ抜き出す
  - GPO内容（XMLレポート）を取得し、「永続化に繋がる設定が既に入っていないか」を検査する（後述）

#### 3-2 リンク（Link）権限：OU/DomainのgPLinkを書けるか（=適用範囲を変えられる）
- 何を確認するか（状態）
  - OU/Domainオブジェクトに対して、主体Sが `gPLink` / `gPOptions` を変更できる（WriteProperty等）か
- 実施（読み取り）
  - OU/DomainのDACLを抽出し、「GPLink相当の書き込み権限が委任されている主体」を特定する（ここが運用委任で崩れやすい）
- 判断（次の一手）
  - 「リンク権限はあるが編集権限がない」場合：
    - 既存の強いGPO（例：管理サーバ設定）を別OUへリンクしてしまう、リンク順序を変える、強制を付け替える等で影響が出得るため、リンク権限単体でも高リスクになり得る。
  - 「編集権限はあるがリンク範囲が狭い」場合：
    - 影響は限定されるが、対象OUが基幹サーバなら重大。OU内の資産価値で優先度を決める。

#### 3-3 作成（Create）権限：新規GPOを作れるか（=攻撃面が拡がる）
- 何を確認するか（状態）
  - 主体SがGPOを新規作成できる（例：作成委任グループに含まれる）
  - さらに「作ったGPOをどこにリンクできるか（=Link権限）」がセットで揃うと影響が広がる
- 判断（次の一手）
  - CreateとLinkが両方揃う主体は、永続化の成立条件として優先度が高い（“好きなOUに自作GPOを刺せる”）

---

### Step 4：GPO内容の“永続化要素”を検査する（変更せずに危険度を上げる）
> 実務で価値が出るのは「どのGPOが危険な設定を配れる状態か」を、証跡付きで言えること。

#### 4-1 GPOレポート（XML/HTML）を作り、危険キーワードで機械的に当てる
- 目的：人間の目視だけにせず、再現性ある手順にする
~~~~
# 例：GPOレポートをXMLで出力→文字列検索で“設定カテゴリ”を当てる
Get-GPOReport -Guid <GPO_GUID> -ReportType Xml -Path .\gpo_<GPO_GUID>.xml

# 例：永続化/遠隔操作/防御低下に繋がりやすいカテゴリ（環境で調整）
# - ScheduledTasks / Services / Scripts / Registry / Groups / RestrictedGroups / SecurityOptions / Firewall / WinRM / RDP など
~~~~
- 判断（次の一手）
  - 「危険カテゴリが含まれる」×「影響範囲が広い」×「編集権限が緩い」＝最優先
  - 「危険カテゴリが含まれない」でも、編集権限が緩いGPOは“将来の注入点”なので是正候補

#### 4-2 SYSVOL（GPT）側の実体チェック（読み取り）
- 目的：GPOが“実際に配るファイル”を確認し、スクリプト/Preferencesの存在を事実化
~~~~
# Linux例（読み取り）：SYSVOLのPolicies配下を参照
# smbclient // <DC_or_DOMAIN>/SYSVOL -U <user>
# smb: \> cd <domain>\Policies\{GPO_GUID}\
# smb: \> ls
# 重点：GPT.INI / Machine\ / User\ / (Preferences配下のXMLなど)

# Windows例（読み取り）：UNCパスで参照
# dir \\<domain>\SYSVOL\<domain>\Policies\{GPO_GUID}\
~~~~
- 判断（次の一手）
  - スクリプトが存在するGPOは、変更された場合の影響が直感的に大きい（“実行”に直結しやすい）ため、権限・監査の優先度を上げる

#### 4-3 GPC→GPT参照先（gPCFileSysPath）の逸脱確認（参照境界）
- 目的：クライアントが“どこからGPTを取るか”を示す参照先が想定外になっていないか
