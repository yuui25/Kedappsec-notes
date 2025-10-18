# PTES Technical Guidelines（PTES 技術ガイドライン）

**ナビゲーションへ移動**  
**検索へ移動**

このセクションは、ペネトレーションテストの実施中に従うべき特定の手順を定義するのに役立つ **PTES の技術ガイドライン** として設計されています。注意すべき点として、ここにあるのは業界で用いられてきた **ベースラインの手法** に過ぎないということです。これらは、コミュニティによっても、あなた自身の標準の中でも、継続的に更新・変更されていく必要があります。ガイドラインとはあくまでガイドラインであり、特定の状況で進むべき方向を示し支援するためのものであって、ペネトレーションテストのやり方を包括的に規定する **完全な手順書ではありません**。**発想を柔軟に** 持ちましょう。

---

## 目次（Contents）

**1　必要なツール（Tools Required）**  
1.1　オペレーティングシステム（Operating Systems）  
1.1.1　MacOS X  
1.1.2　VMware Workstation  
1.1.2.1　Linux  
1.1.2.2　Windows XP/7  
1.2　無線周波数ツール（Radio Frequency Tools）  
1.2.1　周波数カウンタ（Frequency Counter）  
1.2.2　周波数スキャナ（Frequency Scanner）  
1.2.3　スペクトラムアナライザ（Spectrum Analyzer）  
1.2.4　802.11 USB アダプタ（802.11 USB adapter）  
1.2.5　外部アンテナ（External Antennas）  
1.2.6　USB GPS  
1.3　ソフトウェア（Software）

**2　情報収集（Intelligence Gathering）**  
2.1　OSINT  
2.1.1　企業（Corporate）  
2.1.2　物理（Physical）  
2.1.2.1　所在地（Locations）  
2.1.2.2　共有／個別（Shared/Individual）  
2.1.2.3　所有者（Owner）  
2.1.2.3.1　土地／税記録（Land/tax records）  
2.1.3　データセンターの所在（Datacenter Locations）  
2.1.3.1　タイムゾーン（Time zones）  
2.1.3.2　オフサイトでの収集（Offsite gathering）  
2.1.3.3　製品／サービス（Product/Services）  
2.1.3.4　会社の重要日（Company Dates）  
2.1.3.5　役職の特定（Position identification）  
2.1.3.6　組織図（Organizational Chart）  
2.1.3.7　コーポレートコミュニケーション（Corporate Communications）  
2.1.3.7.1　マーケティング（Marketing）  
2.1.3.7.2　訴訟（Lawsuits）  
2.1.3.7.3　取引（Transactions）  
2.1.3.8　求人（Job openings）  
2.1.4　関係性（Relationships）  
2.1.4.1　慈善団体の関係（Charity Affiliations）  
2.1.4.2　ネットワークプロバイダ（Network Providers）  
2.1.4.3　ビジネスパートナー（Business Partners）  
2.1.4.4　競合（Competitors）  

2.2　個人（Individuals）  
2.2.1　ソーシャルネットワークのプロフィール（Social Networking Profile）  
2.2.2　ソーシャルネットワーキングサイト（Social Networking Websites）  
2.2.3　Cree.py  

2.3　インターネット上のフットプリント（Internet Footprint）  
2.3.1　メールアドレス（Email addresses）  
2.3.1.1　Maltego  
2.3.1.2　TheHarvester  
2.3.1.3　NetGlub  
2.3.2　ユーザー名／ハンドル（Usernames/Handles）  
2.3.3　ソーシャルネットワーク（Social Networks）  
2.3.3.1　ニュースグループ（Newsgroups）  
2.3.3.2　メーリングリスト（Mailing Lists）  
2.3.3.3　チャットルーム（Chat Rooms）  
2.3.3.4　フォーラム検索（Forums Search）  
2.3.4　個人ドメイン名（Personal Domain Names）  
2.3.5　個人の活動（Personal Activities）  
2.3.5.1　音声（Audio）  
2.3.5.2　動画（Video）  
2.3.6　アーカイブ情報（Archived Information）  
2.3.7　電子データ（Electronic Data）  
2.3.7.1　ドキュメント漏えい（Document leakage）  
2.3.7.2　メタデータ漏えい（Metadata leakage）  
2.3.7.2.1　FOCA（Windows）  
2.3.7.2.2　Foundstone SiteDigger（Windows）  
2.3.7.2.3　Metagoofil（Linux/Windows）  
2.3.7.2.4　Exif Reader（Windows）  
2.3.7.2.5　ExifTool（Windows/OS X）  
2.3.7.2.6　画像検索（Image Search）  

2.4　秘匿的収集（Covert gathering）  
2.4.1　現地での収集（On-location gathering）  
2.4.1.1　隣接施設（Adjacent Facilities）  
2.4.1.2　物理セキュリティ点検（Physical security inspections）  
2.4.1.2.1　警備員（Security guards）  
2.4.1.2.2　バッジの使用（Badge Usage）  
2.4.1.2.3　施錠装置（Locking devices）  
2.4.1.2.4　侵入検知システム（IDS）／アラーム（Intrusion detection systems (IDS)/Alarms）  
2.4.1.2.5　防犯照明（Security lighting）  
2.4.1.2.6　監視／CCTV システム（Surveillance /CCTV systems）  
2.4.1.2.7　入退室管理装置（Access control devices）  
2.4.1.2.8　環境設計（Environmental Design）  
2.4.1.3　従業員の挙動（Employee Behavior）  
2.4.1.4　ダンプスターダイビング（廃棄物漁り）（Dumpster diving）  
2.4.1.5　RF／無線周波数スキャン（RF / Wireless Frequency scanning）  
2.4.2　周波数の利用状況（Frequency Usage）  
2.4.3　機器の識別（Equipment Identification）  
2.4.3.1　Airmon-ng  
2.4.3.2　Airodump-ng  
2.4.3.3　Kismet-Newcore  
2.4.3.4　inSSIDer  

2.5　外部フットプリンティング（External Footprinting）  
2.5.1　IP 範囲の特定（Identifying IP Ranges）  
2.5.1.1　WHOIS ルックアップ（WHOIS lookup）  
2.5.1.2　BGP ルッキンググラス（BGP looking glasses）  
2.5.2　アクティブ偵察（Active Reconnaissance）  
2.5.3　パッシブ偵察（Passive Reconnaissance）  
2.5.4　アクティブ・フットプリンティング（Active Footprinting）  
2.5.4.1　ゾーン転送（Zone Transfers）  
2.5.4.1.1　host  
2.5.4.1.2　dig  
2.5.4.2　逆引き DNS（Reverse DNS）  
2.5.4.3　DNS ブルート（DNS Bruting）  
2.5.4.3.1　Fierce2（Linux）  
2.5.4.3.2　DNSEnum（Linux）  
2.5.4.3.3　Dnsdict6（Linux）  
2.5.4.4　ポートスキャン（Port Scanning）  
2.5.4.4.1　Nmap（Windows/Linux）  
2.5.4.5　SNMP スイープ（SNMP Sweeps）  
2.5.4.5.1　SNMPEnum（Linux）  
2.5.4.6　SMTP バウンスバック（SMTP Bounce Back）  
2.5.4.7　バナーグラビング（Banner Grabbing）  
2.5.4.7.1　HTTP  

2.6　内部フットプリンティング（Internal Footprinting）  
2.6.1　アクティブ・フットプリンティング（Active Footprinting）  
2.6.1.1　Ping スイープ（Ping Sweeps）  
2.6.1.1.1　Nmap（Windows/Linux）  
2.6.1.1.2　Alive6（Linux）  
2.6.1.2　ポートスキャン（Port Scanning）  
2.6.1.2.1　Nmap（Windows/Linux）  
2.6.1.3　SNMP スイープ（SNMP Sweeps）  
2.6.1.3.1　SNMPEnum（Linux）  
2.6.1.4　Metasploit  
2.6.1.5　ゾーン転送（Zone Transfers）  
2.6.1.5.1　host  
2.6.1.5.2　dig  
2.6.1.6　SMTP バウンスバック（SMTP Bounce Back）  
2.6.1.7　逆引き DNS（Reverse DNS）  
2.6.1.8　バナーグラビング（Banner Grabbing）  
2.6.1.8.1　HTTP  
2.6.1.8.2　httprint  
2.6.1.9　VoIP マッピング（VoIP mapping）  
2.6.1.9.1　内線（Extensions）  
2.6.1.9.2　Svwar  
2.6.1.9.3　enumIAX  
2.6.1.10　パッシブ偵察（Passive Reconnaissance）  
2.6.1.10.1　パケットスニッフィング（Packet Sniffing）

**3　脆弱性分析（Vulnerability Analysis）**  
3.1　脆弱性テスト（Vulnerability Testing）  
3.1.1　アクティブ（Active）  
3.1.2　自動化ツール（Automated Tools）  
3.1.2.1　ネットワーク／汎用脆弱性スキャナ（Network/General Vulnerability Scanners）  
3.1.2.2　Open Vulnerability Assessment System（OpenVAS）（Linux）  
3.1.2.3　Nessus（Windows/Linux）  
3.1.2.4　NeXpose  
3.1.2.5　eEYE Retina  
3.1.2.6　Qualys  
3.1.2.7　Core IMPACT  
3.1.2.7.1　Core IMPACT Web  
3.1.2.7.2　Core IMPACT WiFi  
3.1.2.7.3　Core IMPACT Client Side  
3.1.2.7.4　Core Web  
3.1.2.7.5　coreWEBcrawl  
3.1.2.7.6　Core Onestep Web RPTs  
3.1.2.7.7　Core WiFi  
3.1.2.8　SAINT  
3.1.2.8.1　SAINTscanner  
3.1.2.8.2　SAINTexploit  
3.1.2.8.3　SAINTwriter  
3.1.3　Web アプリケーションスキャナ（Web Application Scanners）  
3.1.3.1　汎用 Web アプリケーションスキャナ（General Web Application Scanners）  
3.1.3.1.1　WebInspect（Windows）  
3.1.3.1.2　IBM AppScan  
3.1.3.1.3　Web ディレクトリ一覧化／ブルートフォース（Web Directory Listing/Bruteforcing）  
3.1.3.1.4　Web サーバのバージョン／脆弱性特定（Webserver Version/Vulnerability Identification）  
3.1.3.2　NetSparker（Windows）  
3.1.3.3　特化型脆弱性スキャナ（Specialized Vulnerability Scanners）  
3.1.3.3.1　VPN（Virtual Private Networking）  
3.1.3.3.2　IPv6  
3.1.3.3.3　ウォーダイヤリング（War Dialing）  
3.1.4　パッシブテスト（Passive Testing）  
3.1.4.1　自動化ツール（Automated Tools）  
3.1.4.1.1　トラフィック監視（Traffic Monitoring）  
3.1.4.2　Wireshark  
3.1.4.3　Tcpdump  
3.1.4.4　Metasploit スキャナ（Metasploit Scanners）  
3.1.4.4.1　Metasploit Unleashed  
3.2　脆弱性の妥当性確認（Vulnerability Validation）  
3.2.1　公開調査（Public Research）  
3.2.1.1　一般的／デフォルトパスワード（Common/default passwords）  
3.2.2　対象リストの作成（Establish target list）  
3.2.2.1　バージョン対応付け（Mapping Versions）  
3.2.2.2　パッチレベルの特定（Identifying Patch Levels）  
3.2.2.3　脆弱な Web アプリの探索（Looking for Weak Web Applications）  
3.2.2.4　脆弱なポートとサービスの特定（Identify Weak Ports and Services）  
3.2.2.5　アカウントロックアウト閾値の特定（Identify Lockout threshold）  
3.3　攻撃経路（Attack Avenues）  
3.3.1　アタックツリーの作成（Creation of Attack Trees）  
3.3.2　防御機構の特定（Identify protection mechanisms）  
3.3.2.1　ネットワーク防御（Network protections）  
3.3.2.1.1　「単純な」パケットフィルタ（"Simple" Packet Filters）  
3.3.2.1.2　トラフィック整形装置（Traffic shaping devices）  
3.3.2.1.3　DLP（Data Loss Prevention）システム  
3.3.2.2　ホストベース防御（Host based protections）  
3.3.2.2.1　スタック／ヒープ保護（Stack/heap protections）  
3.3.2.2.2　ホワイトリスティング（Whitelisting）  
3.3.2.2.3　AV／フィルタリング／振る舞い分析（AV/Filtering/Behavioral Analysis）  
3.3.2.3　アプリケーション層の防御（Application level protections）

**4　エクスプロイト（Exploitation）**  
4.1　精密攻撃（Precision strike）  
4.1.1　対策回避（Countermeasure Bypass）  
4.1.1.1　AV  
4.1.1.2　人的要素（Human）  
4.1.1.3　HIPS  
4.1.1.4　DEP  
4.1.1.5　ASLR  
4.1.1.6　VA + NX（Linux）  
4.1.1.7　w^x（OpenBSD）  
4.1.1.8　WAF  
4.1.1.9　スタックカナリア（Stack Canaries）  
4.1.1.9.1　Microsoft Windows  
4.1.1.9.2　Linux  
4.1.1.9.3　Mac OS  

4.2　カスタマイズされたエクスプロイト（Customized Exploitation）  
4.2.1　ファジング（Fuzzing）  
4.2.2　ダムファジング（Dumb Fuzzing）  
4.2.3　インテリジェントファジング（Intelligent Fuzzing）  
4.2.4　スニッフィング（Sniffing）  
4.2.4.1　Wireshark  
4.2.4.2　Tcpdump  
4.2.5　総当たり（Brute-Force）  
4.2.5.1　Brutus（Windows）  
4.2.5.2　Web Brute（Windows）  
4.2.5.3　THC-Hydra／XHydra  
4.2.5.4　Medusa  
4.2.5.5　Ncrack  
4.2.6　ルーティングプロトコル（Routing protocols）  
4.2.7　Cisco Discovery Protocol（CDP）  
4.2.8　Hot Standby Router Protocol（HSRP）  
4.2.9　Virtual Switch Redundancy Protocol（VSRP）  
4.2.10　Dynamic Trunking Protocol（DTP）  
4.2.11　Spanning Tree Protocol（STP）  
4.2.12　Open Shortest Path First（OSPF）  
4.2.13　RIP  
4.2.14　VLAN ホッピング（VLAN Hopping）  
4.2.15　VLAN Trunking Protocol（VTP）

4.3　無線（RF）アクセス（RF Access）  
4.3.1　暗号化されていない無線 LAN（Unencrypted Wireless LAN）  
4.3.1.1　iwconfig（Linux）  
4.3.1.2　Windows（XP/7）  
4.3.2　アクセスポイントへの攻撃（Attacking the Access Point）  
4.3.2.1　サービス拒否（DoS）  
4.3.3　パスワード解読（Cracking Passwords）  
4.3.3.1　WPA-PSK／WPA2-PSK  
4.3.3.2　WPA／WPA2-Enterprise  
4.3.4　各種攻撃（Attacks）  
4.3.4.1　LEAP  
4.3.4.1.1　Asleap  
4.3.4.2　802.1X  
4.3.4.2.1　鍵配布攻撃（Key Distribution Attack）  
4.3.4.2.2　RADIUS なりすまし攻撃（RADIUS Impersonation Attack）  
4.3.4.3　PEAP  
4.3.4.3.1　RADIUS なりすまし攻撃  
4.3.4.3.2　認証攻撃（Authentication Attack）  
4.3.4.4　EAP-Fast  
4.3.4.5　WEP／WPA／WPA2  
4.3.4.6　Aircrack-ng

4.4　ユーザに対する攻撃（Attacking the User）  
4.4.1　Karmetasploit 攻撃  
4.4.2　DNS リクエスト（DNS Requests）  
4.4.3　Bluetooth  
4.4.4　パーソナライズドな不正 AP（Personalized Rogue AP）  
4.4.5　Web  
4.4.5.1　SQL インジェクション（SQLi）  
4.4.5.2　XSS  
4.4.5.3　CSRF  
4.4.6　アドホックネットワーク（Ad-Hoc Networks）  
4.4.7　検知回避（Detection bypass）  
4.4.8　コントロールの耐攻撃性（Resistance of Controls to attacks）  
4.4.9　攻撃の種類（Type of Attack）  
4.4.10　The Social-Engineer Toolkit

4.5　VPN 検知（VPN detection）  
4.6　ルートの検知（静的ルートを含む）（Route detection, including static routes）  
4.6.1　使用中のネットワークプロトコル（Network Protocols in use）  
4.6.2　使用中のプロキシ（Proxies in use）  
4.6.3　ネットワーク構成（Network layout）  
4.6.4　高価値／高プロファイルのターゲット（High value/profile targets）

4.7　略奪（Pillaging）  
4.7.1　ビデオカメラ（Video Cameras）  
4.7.2　データ持ち出し（Data Exfiltration）  
4.7.3　共有の特定（Locating Shares）  
4.7.4　音声取得（Audio Capture）  
4.7.5　高価値ファイル（High Value Files）  
4.7.6　データベース列挙（Database Enumeration）  
4.7.7　Wi-Fi  
4.7.8　ソースコードリポジトリ（Source Code Repos）  
4.7.9　Git  
4.7.10　カスタムアプリの識別（Identify custom apps）  
4.7.11　バックアップ（Backups）

4.8　ビジネス影響を与える攻撃（Business impact attacks）  
4.9　インフラへのさらなる侵入（Further penetration into infrastructure）  
4.9.1　内部でのピボット（Pivoting inside）  
4.9.1.1　履歴／ログ（History/Logs）  
4.9.2　クリーンアップ（Cleanup）  
4.10　永続化（Persistence）

**5　ポストエクスプロイト（Post Exploitation）**  
5.1　Windows ポストエクスプロイト（Windows Post Exploitation）  
5.1.1　ブラインドファイル（Blind Files）  
5.1.2　非対話型コマンド実行（Non Interactive Command Execution）  
5.1.3　System  
5.1.4　ネットワーク（ipconfig, netstat, net）  
5.1.5　設定（Configs）  
5.1.6　重要ファイルの特定（Finding Important Files）  
5.1.7　取得すべきファイル（可能なら）（Files To Pull (if possible)）  
5.1.8　リモートシステムアクセス（Remote System Access）  
5.1.9　自動起動ディレクトリ（Auto-Start Directories）  
5.1.10　バイナリ・プランティング（Binary Planting）  
5.1.11　ログの削除（Deleting Logs）  
5.1.12　ソフトウェア（アンチウイルス）のアンインストール（非対話）（Uninstalling Software “AntiVirus” (Non interactive)）  
5.1.13　その他（Other）  
5.1.13.1　OS 固有（Operating Specific）  
5.1.13.1.1　Win2k3  
5.1.13.1.2　Vista/7  
5.1.13.1.3　Vista SP1/7/2008/2008R2（x86 & x64）  
5.1.14　侵襲的／変更を伴うコマンド（Invasive or Altering Commands）  
5.1.15　サポートツールのバイナリ／リンク／使い方（Support Tools Binaries / Links / Usage）  
5.1.15.1　各種ツール（Various tools）  

5.2　Windows でのパスワードハッシュ取得（Obtaining Password Hashes in Windows）  
5.2.1　LSASS インジェクション（LSASS Injection）  
5.2.1.1　Pwdump6 と Fgdump  
5.2.1.2　Meterpreter の Hashdump  
5.2.2　レジストリからのパスワード抽出（Extracting Passwords from Registry）  
5.2.2.1　レジストリからのコピー（Copy from the Registry）  
5.2.2.2　ハッシュの抽出（Extracting the Hashes）  
5.2.3　Meterpreter を用いたレジストリからのパスワード抽出（Extracting Passwords from Registry using Meterpreter）

**6　レポーティング（Reporting）**  
6.1　経営層向け報告（Executive-Level Reporting）  
6.2　技術報告（Technical Reporting）  
6.3　リスクの定量化（Quantifying the risk）  
6.4　成果物（Deliverable）

**7　自作ツール（Custom tools developed）**

**8　付録 A — OpenVAS「Only Safe Checks」ポリシーの作成（Appendix A - Creating OpenVAS "Only Safe Checks" Policy）**  
8.1　一般（General）  
8.2　プラグイン（Plugins）  
8.3　認証情報（Credentials）  
8.4　ターゲット選択（Target Selection）  
8.5　アクセスルール（Access Rules）  
8.6　設定（Preferences）  
8.7　ナレッジベース（Knowledge Base）

**9　付録 B —「Only Safe Checks」ポリシーの作成（Appendix B - Creating the "Only Safe Checks" Policy）**  
9.1　一般（General）  
9.2　認証情報（Credentials）  
9.3　プラグイン（Plugins）  
9.4　設定（Preferences）

**10　付録 C —「Only Safe Checks（Web）」ポリシーの作成（Appendix C - Creating the "Only Safe Checks (Web)" Policy）**  
10.1　一般（General）  
10.2　認証情報（Credentials）  
10.3　プラグイン（Plugins）  
10.4　設定（Preferences）

**11　付録 D —「Validation Scan」ポリシーの作成（Appendix D - Creating the "Validation Scan" Policy）**  
11.1　一般（General）  
11.2　認証情報（Credentials）  
11.3　プラグイン（Plugins）  
11.4　設定（Preferences）

**12　付録 E — NeXpose 既定テンプレート（Appendix E - NeXpose Default Templates）**  
12.1　サービス拒否（Denial of service）  
12.2　ディスカバリスキャン（Discovery scan）  
12.3　ディスカバリスキャン（アグレッシブ）（Discovery scan (aggressive)）  
12.4　Exhaustive  
12.5　フル監査（Full audit）  
12.6　HIPAA コンプライアンス（HIPAA compliance）  
12.7　インターネット DMZ 監査（Internet DMZ audit）  
12.8　Linux RPMs  
12.9　Microsoft ホットフィックス（Microsoft hotfix）  
12.10　PCI 監査（Payment Card Industry (PCI) audit）  
12.11　ペネトレーションテスト（Penetration test）  
12.12　ペネトレーションテスト（Penetration test）  
12.13　安全なネットワーク監査（Safe network audit）  
12.14　SOX コンプライアンス（Sarbanes-Oxley (SOX) compliance）  
12.15　SCADA 監査（SCADA audit）  
12.16　Web 監査（Web audit）

# 必要なツール（Tools Required）

以下は、ペネトレーションテスト中に必要となるツールの選定に関する説明です。選定は、エンゲージメントの種類や深度によって変わりますが、一般的に期待される成果を得るために次のツール群は必須と考えられます。

## 1　必要なツール（Tools Required）

### 1.1　オペレーティングシステム（Operating Systems）
ペネトレーションテストで成功裏にネットワークや関連システムを攻撃・悪用するには、利用するオペレーティングプラットフォームの選択が重要です。複数 OS を同時に使えることが求められるため、仮想化が必須となります。

#### 1.1.1　MacOS X
MacOS X は BSD 系に由来するオペレーティングシステムです。標準のシェル（sh、csh、bash 等）や、telnet、ftp、rpcinfo、snmpwalk、host、dig といったネイティブのネットワークユーティリティが利用可能で、ペネトレーションテストに適しています。本環境は我々のペネトレーションテストツールのホストとして選択されることが多く、ハードウェアプラットフォームでもあるため、特定ハードを選ぶことでツールの動作互換性が保たれます。

#### 1.1.2　VMware Workstation
VMware Workstation はワークステーション上で複数の OS インスタンスを容易に動かすために必須です。商用の完全サポート版で、スナップショットや暗号化機能を備えています。無料版にはない暗号化機能は、VM 上に収集した機密情報を保護するために重要であり、暗号化機能を持たないバージョンは使用しないでください。下記に示す OS はゲストとして VMware 上で実行することが推奨されます。

##### 1.1.2.1　Linux
Linux は多くのセキュリティコンサルタントに選ばれるプラットフォームです。カーネルが先端技術やプロトコルの低レベルサポートを提供し、ほとんどの IP ベースの攻撃／ペネトレーションツールがビルド・実行可能です。このため、BackTrack（注：歴史的なディストリビューション）など、ペネトレーション用にツール群がまとまったディストリビューションが好まれます。

##### 1.1.2.2　Windows XP/7
Windows XP/7 は一部ツールの実行に必要です。Microsoft 固有のネットワーク評価ツールや商用ツールの多くがこのプラットフォーム上で動作します。

### 1.2　無線周波数ツール（Radio Frequency Tools）
RF（無線周波数）評価を行う際に必要となる機器群です。

#### 1.2.1　周波数カウンタ（Frequency Counter）
周波数カウンタは 10Hz 〜 3GHz をカバーできるものが望ましいです。価格対性能比の良い例として MFJ-886 Frequency Counter が挙げられます。

#### 1.2.2　周波数スキャナ（Frequency Scanner）
スキャナは複数の離散周波数を自動でチューニングし、信号を検出した場合に停止して表示する無線受信機です。ただし、各州や国の法規制に注意が必要で、例として米国では Florida, Kentucky, Minnesota ではアマチュア無線免許保有者でなければ使用禁止の場合があります。推奨ハードウェア例は Uniden BCD396T Bearcat Handheld Digital Scanner や PSR-800 GRE Digital trunking scanner です。

#### 1.2.3　スペクトラムアナライザ（Spectrum Analyzer）
スペクトラムアナライザは電気的・音響的・光学的波形のスペクトル構成を調べるための機器です。無線送信機が規定された基準に従って動作しているか、あるいは信号の帯域幅を直接観測して判断するために用います。手頃な例として Kaltman Creations HF4060 RF Spectrum Analyzer があります。

#### 1.2.4　802.11 USB アダプタ（802.11 USB adapter）
802.11 USB アダプタは、ペネトレーションテスト用システムに無線アダプタを簡便に接続するために使用します。全ての USB アダプタが必要な機能（モニターモードやパケット注入など）をサポートするわけではないため、適合する製品を選定してください。推奨ハードウェアは Alfa AWUS051NH 500mW High Gain 802.11a/b/g/n high power Wireless USB です。

#### 1.2.5　外部アンテナ（External Antennas）
外部アンテナは用途に応じて形状やコネクタが様々です。Alfa に接続するには RP-SMA コネクタが必要です。Alfa は通常オムニ方向性アンテナが付属するため、方向性が必要な場合はパネルアンテナ（指向性）が適しています。例：L-com 2.4 GHz 14 dBi Flat Panel Antenna with RP-SMA connector。また、携行性を考慮した磁石マウント式オムニ（例：L-com 2.4 GHz/900 MHz 3 dBi Omni Magnetic Mount Antenna with RP-SMA Plug Connector）も有用です。

#### 1.2.6　USB GPS
RF 評価で信号の伝播範囲や位置を正確に記録するには GPS が必須です。使用する OS（Linux/Windows/MacOS）でサポートされる USB GPS を選択してください。

### 1.3　ソフトウェア（Software）
ソフトウェア要件はエンゲージメントのスコープに依存しますが、フルなペネトレーションテストを適切に実施するために、以下の商用・オープンソースソフトの例を挙げます。

| ソフトウェア (Software) | URL | 説明 (Description) | Windows 専用 (Windows Only) |
|---|---|---|:---:|
| Maltego | http://www.paterva.com/web5 | 情報収集（人物・企業のデータマイニング）の事実上の標準ツール。Community（無料）版と有償版がある。 |  |
| Nessus | http://tenable.com/products/nessus | 脆弱性スキャナ（有償/無償版あり）。主に内部ネットワークの脆弱性発見と文書化に有用。 |  |
| IBM AppScan | http://www-01.ibm.com/software/awdtools/appscan | IBM の自動化された Web アプリケーションセキュリティテストスイート。 | * |
| eEye Retina | http://www.eeye.com/Products/Retina.aspx | Retina は自動ネットワーク脆弱性スキャナで、Web ベースのコンソールで管理できる。Metasploit と連携して、既知のエクスプロイトが存在する場合に直接発動して検証できる。 |  |
| Nexpose | http://www.rapid7.com | Metasploit を提供する会社の脆弱性スキャナ。無償/有償でサポートや機能差がある。 |  |
| OpenVAS | http://www.openvas.org | Nessus から派生した脆弱性スキャナ。日次更新される多数の NVT（Network Vulnerability Tests）を備える。 |  |
| HP WebInspect | https://www.fortify.com/products/web_inspect.html | 複雑な Web アプリケーションのセキュリティ評価を行う商用ツール。JavaScript, Flash, Silverlight 等をサポート。 | * |
| HP SWFScan | https://h30406.www3.hp.com/campaigns/2009/wwcampaign/1-5TUVE/index.php?key=swf | Flash アプリケーションの脆弱性検出に使える無料ツール。Flash の逆コンパイルやハードコードされた資格情報などの解析に有用。 | * |
| BackTrack Linux | [1] | ペネトレーション用ツールを多数収録した Linux ディストリビューション（歴史的）。Live CD/USB/VM での運用が可能。 |  |
| SamuraiWTF (Web Testing Framework) | http://samurai.inguardians.com | Web アプリケーション用のライブ Linux ディストリビューション。Fierce, Maltego, WebScarab, BeEF 等を含む。 |  |
| SiteDigger | http://www.mcafee.com/us/downloads/free-tools/sitedigger.aspx | Windows 向けの無料ツール。Google キャッシュを利用して脆弱性、エラー、設定ミス、公開文書から興味深い情報を探す。 | * |
| FOCA | http://www.informatica64.com/DownloadFOCA | 公開ドキュメントのメタデータ解析によりサイトの情報を収集するツール。 | * |
| THC IPv6 Attack Toolkit | http://www.thc.org/thc-ipv6 | IPv6 と ICMP6 の脆弱性を突くツール群。 |  |
| THC Hydra | http://thc.org/thc-hydra/ | 高速なログオンブルートフォースツール。多くのサービスに対して並列攻撃が可能。 | * |
| Cain | http://www.oxid.it/cain.html | Windows 向けのパスワードリカバリツール。ネットワークスニッフィング、辞書/ブルートフォース、VoIP録音、ハッシュ解析など多機能。 | * |
| cree.py | http://ilektrojohn.github.com/creepy/ | SNS や画像ホスティングから位置情報を収集し、地図上に表示するツール。 |  |
| inSSIDer | http://www.metageek.net/products/inssider | Windows 用の GUI ベース Wi-Fi 発見／トラブルシュートツール。 | * |
| Kismet Newcore | http://kismetwireless.net | 802.11 レイヤ2 無線検出器・スニッファ・侵入検知システム。モニターモード対応アダプタでパッシブにパケット収集が可能。 |  |
| Rainbow Crack | http://project-rainbowcrack.com | レインボーテーブルを用いたハッシュクラッキングツール。 |  |
| dnsenum | http://code.google.com/p/dnsenum | whois を拡張した DNS 列挙ツール。サブドメイン発見や BIND バージョン調査などに利用。 |  |
| dnsmap | http://code.google.com/p/dnsmap | サブドメイン総当たりによる DNS マッピング用の受動的ツール。 |  |
| dnsrecon | http://www.darkoperator.com/tools-and-scripts/ | Ruby で書かれた DNS 列挙スクリプト。TLD 拡張、SRV 列挙、ゾーン転送、逆引き等を行う。 |  |
| dnstracer | http://www.mavetju.org/unix/dnstracer.php | 指定ドメインの DNS 情報の由来をたどるツール。 |  |
| dnswalk | http://sourceforge.net/projects/dnswalk | DNS データベースの整合性チェックを行うデバッガ。ゾーン転送や一貫性チェックを実施。 |  |
| Fierce | http://ha.ckers.org/fierce | ノンコンティグな IP 範囲を探索するドメインスキャンツール。 |  |
| Fierce2 | http://trac.assembla.com/fierce/ | Fierce の更新版。開発者グループによるメンテナンスあり。 |  |
| FindDomains | http://code.google.com/p/finddomains | マルチスレッドのドメイン発見ツール。大量の IP に対応する仮想ホスト/サブドメイン発見に有用。 | * |
| HostMap | http://hostmap.lonerunners.net | 指定 IP に紐づく全ホスト名・仮想ホストを自動発見するツール。 |  |
| URLcrazy | http://www.morningstarsecurity.com/research/urlcrazy/ | ドメインのタイプミス候補を生成し、スカッティングや類似ドメインを発見する。 |  |
| theHarvester | http://www.edge-security.com/theHarvester.php | 検索エンジンや PGP キーサーバ等からメールアドレス／ユーザ名／ホスト名を収集する情報収集ツール。 |  |
| The Metasploit Framework | http://metasploit.com | リモートエクスプロイトやポストエクスプロイテーションツール群の集積。定期的な更新（svn 等）で新機能・エクスプロイトが追加されるため常時更新が望ましい。 |  |
| The Social-Engineer Toolkit (SET) | http://www.secmaniac.com/download/ | 人間要素に対する高度な攻撃を実行するためのフレームワーク。偽メールや偽サイト生成などソーシャルエンジニアリングに特化。 |  |
| Fast-Track | http://www.secmaniac.com/download/ | 自動化されたペンテストスイート。多くの攻撃はクライアント側のデータサニタイズ不備やパッチ未適用に起因。Metasploit 3 に依存。 |  |

（表中の「*」は該当ツールが Windows 専用であることを示しています。表内リンクは参考 URL のまま記載しています。）

---

### 補足
- 上記リストは包括的な推奨例であり、実際のエンゲージメントやスコープ、法規制、予算に応じて調整してください。  
- 無線周波数機器やスキャナの使用には各国・各地域の法規制が適用されます。使用前に必ず現地法令を確認してください。  
- 商用ツールの導入時はライセンス条件や暗号化・データ保護機能の有無を確認し、収集した機密情報を適切に保護してください。

# 情報収集（Intelligence Gathering）

情報収集は、評価（アセスメント）を導くためのデータ（＝「インテリジェンス」）を収集する段階です。最も広い意味では、従業員、施設、製品、計画に関する情報が含まれます。より大きな文脈では、競合他社の秘密または非公開の「インテリジェンス」や、ターゲットに関連するその他の情報も含まれます。

## 2　情報収集（Intelligence Gathering）

### 2.1　OSINT
オープンソース・インテリジェンス（OSINT）は、最も単純化すると、公開（オープン）された情報源を特定・分析することです。ここで重要なのは、この情報収集プロセスの目的が、「攻撃者」または「競合」にとって価値のある、**最新かつ関連性の高い情報**を生み出すことにあります。OSINT は単に複数の検索エンジンでウェブ検索を行う以上の行為を含みます。

#### 2.1.1　企業（Corporate）
特定のターゲットに関する情報には、**法的実体（リーガルエンティティ）**に関する情報を含めるべきです。米国内の多くの州では、株式会社（Corporation）、LLC、リミテッド・パートナーシップ等が州当局に登記・届け出を行うことが求められます。州当局はそれらの書類の保管者であり、提出書類や証明書の写しを保持しています。これらには、株主、メンバー、役員、その他ターゲット実体に関与する人物に関する情報が含まれることがあります。

| State | URL |
|---|---|
| Alabama | http://sos.alabama.gov/BusinessServices/NameRegistration.aspx |
| Alaska | http://www.dced.state.ak.us/bsc/corps.htm |
| Arizona | http://starpas.azcc.gov/scripts/cgiip.exe/WService=wsbroker1/main.p |
| Arkansas | http://www.sosweb.state.ar.us/corps/incorp |
| California | http://kepler.sos.ca.gov/ |
| Colorado | http://www.state.co.us |
| Connecticut | http://www.state.ct.us |
| Delaware | http://www.state.de.us |
| District of Columbia | http://www.ci.washington.dc.us |
| Florida | http://www.sunbiz.org/search.html |
| Georgia | http://corp.sos.state.ga.us/corp/soskb/CSearch.asp |
| Hawaii | http://www.state.hi.us |
| Idaho | http://www.accessidaho.org/public/sos/corp/search.html?SearchFormstep=crit |
| Illinois | http://www.ilsos.gov/corporatellc |
| Indiana | http://secure.in.gov/sos/bus_service/online_corps/default.asp |
| Iowa | http://www.state.ia.us |
| Kansas | http://www.accesskansas.org/apps/corporations.html |
| Kentucky | http://ukcc.uky.edu/~vitalrec |
| Louisiana | http://www.sec.state.la.us/crpinq.htm |
| Maine | http://www.state.me.us/sos/cec/corp/ucc.htm |
| Maryland | http://sdatcert3.resiusa.org/ucc-charter |
| Massachusetts | http://ucc.sec.state.ma.us/psearch/default.asp |
| Michigan | http://www.cis.state.mi.us/bcs_corp/sr_corp.asp |
| Minnesota | http://www.state.mn.us/ |
| Mississippi | http://www.sos.state.ms.us/busserv/corpsnap |
| Missouri | http://www.state.mo.us |
| Montana | http://sos.state.mt.us |
| Nebraska | http://www.sos.state.ne.us/htm/UCCmenu.htm |
| Nevada | http://sandgate.co.clark.nv.us:8498/cicsRecorder/ornu.htm |
| New Hampshire | http://www.state.nh.us |
| New Jersey | http://www.state.nj.us/treasury/revenue/searchucc.htm |
| New Mexico | http://www.sos.state.nm.us/UCC/UCCSRCH.HTM |
| New York | http://wdb.dos.state.ny.us/corp_public/corp_wdb.corp_search_inputs.show |
| North Carolina | http://www.secstate.state.nc.us/research.htm |
| North Dakota | http://www.state.nd.us/sec |
| Ohio | http://serform.sos.state.oh.us/pls/report/report.home |
| Oklahoma | http://www.oklahomacounty.org/coclerk/ucc/default.asp |
| Oregon | http://egov.sos.state.or.us/br/pkg_web_name_srch_inq.login |
| Pennsylvania | http://www.dos.state.pa.us/DOS/site/default.asp |
| Rhode Island | http://155.212.254.78 |
| South Carolina | http://www.scsos.com/corp_search.htm |
| South Dakota | http://www.state.sd.us |
| Tennessee | http://www.state.tn.us/sos/service.htm |
| Texas | https://ourcpa.cpa.state.tx.us/coa/Index.html |
| Utah | http://www.commerce.state.ut.us |
| Vermont | http://www.sec.state.vt.us/seek/database.htm |
| Virginia | http://www.state.va.us |
| Washington | http://www.dol.wa.gov/business/UCC/ |
| West Virginia | http://www.wvsos.com/wvcorporations |
| Wisconsin | http://www.wdfi.org/corporations/crispix |
| Wyoming | http://soswy.state.wy.us/Corp_Search_Main.asp |


#### 2.1.2　物理（Physical）
OSINT の最初のステップは、多くの場合、ターゲット企業の**物理的ロケーション**の特定です。一般公開または広く知られた施設の所在地は容易に得られますが、秘匿されたサイトの特定は容易ではありません。公開サイトの特定には、次のような検索エンジンが利用できます。

- Google — http://www.google.com
- Yahoo — http://yahoo.com
- Bing — http://www.bing.com
- Ask.com — http://ask.com

##### 2.1.2.1　所在地（Locations）
（前述の検索や公開資料から所在地を特定する。）

##### 2.1.2.2　共有／個別（Shared/Individual）
所在地が**単独の建物**なのか、**大型施設の一部（スイート）**なのかを把握することが重要です。隣接する事業者や共用部（共用スペース）についても可能な限り把握します。

##### 2.1.2.3　所有者（Owner）
物理ロケーションを特定したら、その**不動産の実所有者**（個人・団体・法人）を特定することが有用です。ターゲット企業が不動産の所有者でない場合、物理的な改善・強化に制約が生じる可能性があります。

###### 2.1.2.3.1　土地／税記録（Land/tax records）
**税記録：**  
http://www.naco.org/Counties/Pages/CitySearch.aspx

**土地・税記録**には、所有権、占有、抵当権者、差押・差し押さえ通知、写真など、ターゲットに関する多くの情報が含まれる場合があります。公開範囲や透明性の度合いは管轄によって大きく異なります。米国では、土地・税記録は一般に**郡（カウンティ）**レベルで扱われます。

開始手順の一例：ターゲットの所在する**都市名や郵便番号**が分かっている場合は、まず http://publicrecords.netronline.com/ を使って該当する**郡**を特定します。その後、Google で  
「`XXXX county tax records`」「`XXXX county recording office`」「`XXXX county assessor`」  
などのクエリを用いると、オンライン検索可能なデータベース（存在する場合）に到達できます。オンラインに存在しない場合でも、郡の登記事務所に**電話**し、目的の記録を特定できれば**FAX での送付**を依頼できる場合があります。

**建築局（Building department）：**  
評価の目的によっては、さらに一歩進めて**建築局**に問い合わせを行うことが適切な場合もあります。ターゲットの所在地が**市**と**郡**のどちらの管轄かは、いずれかへ電話確認することで多くの場合判明します。

建築局には、**平面図（フロアプラン）**、**過去／現在の許可（permit）**、**テナント改修情報**等が保管されています。埋もれた情報の中には、**施工会社、エンジニア、建築家の名称**などが含まれる場合があり、これらは SET（Social-Engineer Toolkit）等のツールと組み合わせて利用可能です。多くの場合、入手には電話連絡が必要ですが、たいていの建築局は**求めがあれば提供**してくれます。

**（入手のための想定プレテキストの一例）**  
「建物のリモデリングや増築の設計を依頼された**建築コンサルタント**で、作業を円滑に進めるために**オリジナルの設計図**を入手したい」と伝える方法が考えられます。

#### 2.1.3　データセンターの所在（Datacenter Locations）
企業ウェブサイト、公的提出書類、土地記録、検索エンジン等から、ターゲットの**データセンター所在地**を特定することは、追加の潜在ターゲットを得るうえで有益です。

##### 2.1.3.1　タイムゾーン（Time zones）
ターゲットが運用する**タイムゾーン**を特定することで、**営業時間**に関する重要な情報が得られます。また、ターゲットのタイムゾーンと**評価チームのタイムゾーンの関係**を理解することも重要です。テスト実施時には**タイムゾーンマップ**を参照として用いると便利です。

（TimeZone Map）

##### 2.1.3.2　オフサイトでの収集（Offsite gathering）
企業ウェブサイトや検索エンジンを通じて、最近または将来予定される**オフサイトの集まりやパーティ**を特定することで、ターゲットの**企業文化**に関する洞察が得られます。多くの企業は、従業員だけでなく**ビジネスパートナーや顧客**向けにもオフサイトイベントを開催します。こうしたデータの収集は、攻撃者が関心を持ちうる対象を見極めるうえで有益です。

##### 2.1.3.3　製品／サービス（Product/Services）
企業サイト、ニュースリリース、検索エンジンなどから、ターゲットの**製品・サービス**や**重要な発表**を特定すると、内部の動向に関する洞察が得られます。多くの企業は、宣伝や既存・新規顧客への周知のために、こうした通知を**公開**します。公開情報には、**多言語文書、ラジオ・テレビ放送、ウェブサイト、講演**などが含まれます。

##### 2.1.3.4　会社の重要日（Company Dates）
重要な**会社行事**は、スタッフの警戒レベルが通常より高くなる可能性を示唆します。これには、**経営会議、取締役会、投資家向け会議、企業記念日**などが含まれます。一般に、各種**休日**を遵守する企業では、スタッフが大幅に減り、期間中の**標的化は難しく**なる傾向があります。

##### 2.1.3.5　役職の特定（Position identification）
あらゆるターゲットにおいて、**組織の上位役職**を特定・文書化することは極めて重要です。これにより、**最終報告書が正しい読者（ステークホルダー）**に向けられることを保証します。少なくとも、**主要従業員**はどのエンゲージメントでも特定しておくべきです。

##### 2.1.3.6　組織図（Organizational Chart）
組織構造の理解は、**縦（深さ）**だけでなく**横（広がり）**を把握するうえでも重要です。非常に大きな組織では、新規スタッフや一部人員が**気づかれずに存在**する可能性がありますが、小規模組織ではその可能性は低くなります。組織図を把握することは、**機能別グループ**の洞察を得る助けにもなり、**内部ターゲットの決定**にも役立ちます。

##### 2.1.3.7　コーポレートコミュニケーション（Corporate Communications）
企業ウェブサイトや**求人検索エンジン**を通じて、**コーポレートコミュニケーション**を特定すると、ターゲットの内部動向に関する有益な洞察が得られます。

###### 2.1.3.7.1　マーケティング（Marketing）
マーケティングコミュニケーションは、**現在または将来の製品リリース**、**提携**等に関する企業発表に用いられます。

###### 2.1.3.7.2　訴訟（Lawsuits）
**訴訟関係**に関するコミュニケーションは、潜在的な**脅威主体**や**関心データ**に関する洞察をもたらす可能性があります。

###### 2.1.3.7.3　取引（Transactions）
**企業取引**に関するコミュニケーションは、マーケティング発表や訴訟への**間接的な反応**として行われる場合があります。

##### 2.1.3.8　求人（Job openings）
企業サイトや**求人検索エンジン**を通じて現在の**求人・募集**を調べると、ターゲットの内部動向に関する洞察が得られます。多くの場合、**現在または今後の技術導入**に関する情報が含まれ、攻撃者が関心を持ちうる項目の把握に役立ちます。以下は例です。

| Site | URL |
|---|---|
| Monster | http://www.monster.com |
| CareerBuilder | http://www.careerbuilder.com |
| Computerjobs.com | http://www.computerjobs.com |
| Craigslist | http://www.craigslist.org/about/sites |

#### 2.1.4　関係性（Relationships）
ターゲットの**論理的な関係性**を特定することは、企業のオペレーションを理解するうえで重要です。公開情報を活用し、**ベンダー、ビジネスパートナー、法律事務所**等との関係を把握します。これは、ニュースリリース、**企業ウェブサイト（ターゲットおよびベンダー側）**、業界関連フォーラムなどで得られることが多いです。

##### 2.1.4.1　慈善団体の関係（Charity Affiliations）
企業サイトや検索エンジンを通じて、ターゲット企業の**慈善団体との関係**を特定すると、内部動向や**企業文化**に関する洞察が得られます。多くの企業は様々な組織に**寄付**を行っています。収集したデータは、攻撃者が関心を持ちうる項目の見極めに役立ちます。

##### 2.1.4.2　ネットワークプロバイダ（Network Providers）
**割り当て済みネットブロック／アドレス情報**、企業サイト、検索エンジン等から、ターゲットの**ネットワーク提供・プロビジョニング**に関する情報を特定することで、ターゲットの可能性に関する洞察が得られます。（注：元文では一部「慈善寄付」に関する記述が繰り返されていますが、ここでは**ネットワーク提供者の特定**に焦点が置かれます。）

##### 2.1.4.3　ビジネスパートナー（Business Partners）
**ビジネスパートナー**を特定することは、ターゲットの企業文化だけでなく、**採用技術**の把握にも重要です。多くの企業は**パートナーシップ契約**を発表します。これらの情報は、攻撃者が関心を持ちうる項目の見極めに役立ちます。

##### 2.1.4.4　競合（Competitors）
**競合他社**の特定は、潜在的な**敵対勢力**を理解するうえで有効です。競合は、ターゲットに影響を与えうるニュース（**人材採用、新製品発表、提携**など）を発表することが珍しくありません。こうしたデータの収集は、潜在的な**企業間の敵対関係**を十分に理解するために重要です。

# 個人（Individuals）

個人に関する情報収集は、従業員や関係者の関係性・行動・公開情報を特定することで、後続フェーズ（ソーシャルエンジニアリング、ターゲティング等）に役立つインテリジェンスを得る段階です。

## 2.2　個人（Individuals）

### 2.2.1　ソーシャルネットワークのプロフィール（Social Networking Profile）
アクティブなソーシャルネットワーキングサイトとユーザ数の多さは、従業員の友情関係、親族関係、共通の興味、金銭のやり取り、好み・嫌い、性的関係、信条を特定するのに最適な場所です。従業員の社内での知識や評判（prestige）すら判別できる場合があります。

### 2.2.2　ソーシャルネットワーキングサイト（Social Networking Websites）
以下は代表的なソーシャルネットワーキングサイト一覧（名称・URL・注記の簡潔版）。調査対象者のプロフィールや投稿、つながり、公開写真、位置情報などを検査する際に参照します。

| 名称 | URL | 説明／フォーカス |
|---|---|---|
| Academia.edu | http://www.academia.edu | 研究者・アカデミック向けのソーシャルネットワーキング |
| Advogato | http://www.advogato.org | フリー／オープンソースソフトウェア開発者向け |
| aNobii | http://www.anobii.com/anobii_home | 本（読書コミュニティ） |
| aSmallWorld | http://www.asmallworld.net | 欧州のジェットセッターと世界中のソーシャルエリート |
| AsianAvenue | http://www.asianave.com | アジア系アメリカ人コミュニティ向け SNS |
| Athlinks | http://www.athlinks.com | ランニング・水泳（オープン大会記録等） |
| Audimated.com | http://www.audimated.com | インディー音楽 |
| Avatars United | http://www.avatarsunited.com | オンラインゲーム |
| Badoo | http://badoo.com | 総合系／出会い、欧州・中南米で人気 |
| Bebo | http://www.bebo.com | 総合系 |
| Bigadda | http://bigb.bigadda.com | インドのソーシャルネットワーキングサイト |
| Federated Media's BigTent | http://www.federatedmedia.net | グループ向けの組織／コミュニケーションポータル |
| Biip.no | http://www.biip.no | ノルウェーのコミュニティ |
| BlackPlanet | http://www.blackplanet.com | アフリカ系アメリカ人向け |
| Blauk | http://blauk.com | 見知らぬ人や知人について何かを語りたい人向け |
| Blogster | http://www.blogster.com | ブログコミュニティ |
| Bolt.com | http://www.bolt.com | 総合系 |
| Buzznet | http://www.buzznet.com | 音楽・ポップカルチャー |
| CafeMom | http://www.cafemom.com | 母親向け |
| Cake Financial | http://www.cakefinancial.com | 投資 |
| Care2 | http://www.care2.com | エコライフと社会的アクティビズム |
| CaringBridge | http://www.caringbridge.org | 重篤な健康事象の間に家族・友人とつながるための非営利ウェブサイト |
| Cellufun | http://m.cellufun.com | モバイルソーシャルゲームネットワーク（米国モバイルサイト第8位） |
| Classmates.com | http://www.classmates.com | 学校・大学・職場・軍関係 |
| Cloob | http://www.cloob.com | 総合系（イランで人気） |
| CouchSurfing | http://www.couchsurfing.org | 旅人と訪問先コミュニティを結ぶ世界的ネットワーク |
| CozyCot | http://www.cozycot.com | 東アジア／東南アジアの女性向け |
| Cross.tv | http://www.cross.tv | クリスチャン向け信仰ベースの SNS（世界中） |
| Crunchyroll | http://www.crunchyroll.com | アニメとフォーラム |
| Cyworld | (Korea) http://cyworld.co.kr / (China) http://www.cyworld.com.cn | 総合系（韓国で人気） |
| DailyBooth | http://dailybooth.com | 毎日1枚写真を投稿するフォトブログ |
| DailyStrength | http://www.dailystrength.org | 医療・メンタルサポートコミュニティ（身体・精神・各種支援グループ） |
| Decayenne | http://www.decayenne.com | 欧米のソーシャルエリート |
| delicious | http://www.delicious.com | ソーシャルブックマーク（興味の合うサイトの発見・保存） |
| deviantART | http://www.deviantart.com | アートコミュニティ |
| Disaboom | http://www.disaboom.com | 障害のある人向け（切断、脳性麻痺、MS 等） |
| Dol2day | http://www.dol2day.de | 政治コミュニティ／SNS／ネットラジオ（独語圏） |
| DontStayIn | http://www.dontstayin.com | クラビング（主に英国） |
| Draugiem.lv | http://www.draugiem.lv | 総合系（主にラトビア・リトアニア・ハンガリー） |
| douban | http://www.douban.com | 中国のレビュー＆レコメンド（映画・本・音楽）／巨大データベース＆コミュニティ |
| Elftown | http://www.elftown.com | ファンタジー／SFを中心としたコミュニティ＆Wiki |
| Entitycube | http://entitycube.research.microsoft.com | （説明なし） |
| Eons.com | http://www.eons.com | ベビーブーマー向け |
| Epernicus | http://www.epernicus.com | 研究科学者向け |
| Experience Project | http://www.experienceproject.com | 人生経験の共有 |
| Exploroo | http://www.exploroo.com | 旅行系ソーシャルネットワーキング |
| Facebook | (IPv4) http://www.facebook.com / (IPv6) http://www.v6.facebook.com | 総合系 |
| Faceparty | http://www.faceparty.com | 総合系（英国で人気） |
| Faces.com | http://www.face-pic.com / http://www.faces.com | 英国のティーン向け |
| Fetlife | http://fetlife.com | BDSM 指向の人向け |
| FilmAffinity | http://www.filmaffinity.com | 映画・TVシリーズ |
| FitFinder | http://www.thefitfinder.co.uk | 英国の匿名学生向けマイクロブログ |
| FledgeWing | http://www.fledgewing.com | 世界の大学生向け起業コミュニティ |
| Flixster | http://www.flixster.com | 映画 |
| Flickr | http://www.flickr.com | 写真共有／コメント／写真系ネットワーキング（世界規模） |
| Focus.com | http://www.focus.com | B2B（世界規模） |
| Folkdirect | http://www.folkdirect.com | 総合系 |
| Fotki | http://www.fotki.com | 写真共有・動画ホスティング・コンテスト・日記・掲示板等、柔軟なプライバシー設定 |
| Fotolog | http://www.fotolog.com | フォトブログ（南米・スペインで人気） |
| Foursquare | http://foursquare.com | 位置情報ベースのモバイル SNS |
| Friends Reunited | http://www.friendsreunited.com | 英国発：学校・大学・職場・スポーツ・地域 |
| Friendster | http://www.friendster.com | 総合系（東南アジアで人気、西洋では衰退） |
| Frühstückstreff | http://www.fruehstueckstreff.de | 総合系 |
| Fubar | http://www.fubar.com | デーティング、18歳以上向け「オンラインバー」 |
| Gaia Online | http://www.gaiaonline.com | アニメ＆ゲーム（米・加・欧で人気、アジアでも中程度） |
| GamerDNA | http://www.gamerdna.com | コンピュータ／ビデオゲーム |
| Gather.com | http://home.gather.com | 記事・写真・動画共有、グループ討議 |
| Gays.com | http://gays.com | LGBT 向け SNS、バー／レストラン等のガイド |
| Geni.com | http://www.geni.com | 家族・系譜 |
| Gogoyoko | http://www.gogoyoko.com | ミュージシャンと音楽ファン向け（フェアプレイ志向） |
| Goodreads | http://www.goodreads.com | 図書カタログ・ブックラバー向け |
| Goodwizz | http://www.goodwizz.com | マッチメイキング＆性格ゲーム、グローバル（仏拠点） |
| Google Buzz | http://www.google.com/buzz | 総合系 |
| Google+ | http://plus.google.com | 総合系 |
| GovLoop | http://www.govloop.com | 行政関係者と周辺の人向け |
| Gowalla | http://gowalla.com | （説明なし） |
| Grono.net | http://grono.net | ポーランド |
| Habbo | http://www.habbo.com | ティーン向け総合（31以上のコミュニティ）／チャット・プロフィール |
| hi5 | http://hi5.com | 総合系（印・モンゴル・タイ・ルーマニア・ジャマイカ・中部アフリカ・ポルトガル・中南米で人気／米国では不人気） |
| Hospitality Club | http://www.hospitalityclub.org | ホスピタリティ（相互宿泊等） |
| Hotlist | http://www.thehotlist.com | 友人の「今・過去・未来」の居場所を可視化する位置系ソーシャル集約 |
| HR.com | http://www.hr.com | 人事（HR）プロ向け SNS |
| Hub Culture | http://www.hubculture.com | 価値創造に焦点を当てたグローバル・インフルエンサー |
| Hyves | http://www.hyves.nl | 総合系（オランダで最も人気） |
| Ibibo | http://www.ibibo.com | タレント発掘・自己プロモの SNS（インドで人気） |
| Identi.ca | http://identi.ca | ハッカー／ソフトウェア自由主義者に人気の Twitter ライク |
| Indaba Music | http://www.indabamusic.com | ミュージシャンのオンライン協業・リミックス・ネットワーキング |
| IRC-Galleria | http://www.irc-galleria.net | フィンランド |
| italki.com | http://www.italki.com | 語学学習 SNS（100以上の言語） |
| InterNations | http://www.internations.org | 国際コミュニティ |
| Itsmy | http://mobile.itsmy.com | 世界規模のモバイルコミュニティ（ブログ・友人・個人TV） |
| iWiW | http://iwiw.hu | ハンガリー |
| Jaiku | http://www.jaiku.com | 総合系／マイクロブログ（Google 所有） |
| JammerDirect.com | http://www.jammerdirect.com | 無所属アーティスト向けネットワーク |
| kaioo | http://www.kaioo.com | 総合系・非営利 |
| Kaixin001 | http://www.kaixin001.com | 総合系（簡体字中国語、主に中国本土向け） |
| Kiwibox | http://www.kiwibox.com | 総合系（ユーザー主導のコミュニティ） |
| Lafango | http://lafango.com | タレント志向のメディア共有サイト |
| Last.fm | http://www.last.fm | 音楽 |
| LibraryThing | http://www.librarything.com/ / http://www.librarything.de | ブックラバー向け |
| Lifeknot | http://www.lifeknot.com | 共有の興味・趣味 |
| LinkedIn | http://www.linkedin.com | ビジネス／プロフェッショナル向け |
| LinkExpats | http://www.linkexpats.com | 駐在・海外在住者向け SNS（100カ国以上） |
| Listography | http://listography.com | リスト作成・自伝 |
| LiveJournal | http://www.livejournal.com | ブログ（ロシアとロシア語圏ディアスポラで人気） |
| Livemocha | http://www.livemocha.com | オンライン語学学習（35言語、世界最大級のネイティブコミュニティ） |
| LunarStorm | http://www.lunarstorm.se | スウェーデン |
| MEETin | http://www.meetin.org | 総合系 |
| Meetup.com | http://www.meetup.com | 総合系（各種オフラインミーティングの企画） |
| Meettheboss | http://www.meettheboss.tv | ビジネス＆ファイナンスコミュニティ（世界規模） |
| Mixi | http://www.mixi.jp | 日本 |
| mobikade | http://www.mkade.com | モバイルコミュニティ（英国のみ） |
| MocoSpace | http://www.mocospace.com | モバイルコミュニティ（世界規模） |
| MOG | http://www.mog.com | 音楽 |
| MouthShut.com | http://www.mouthshut.com | ソーシャルネットワーク／ソーシャルメディア／消費者レビュー |
| Mubi (website) | http://mubi.com | 作家主義（オーターヌーヴォー）系映画 |
| Multiply | http://multiply.com | 現実世界の関係（主にアジアで人気） |
| Muxlim | http://muxlim.com | イスラム系ポータル |
| MyAnimeList | http://www.myanimelist.net | アニメ特化コミュニティ |
| MyChurch | http://www.mychurch.org | キリスト教教会 |
| MyHeritage | http://www.myheritage.com | 家族志向の SNS |
| MyLife | http://www.mylife.com | 友人・家族探しと連絡維持（旧 Reunion.com） |
| My Opera | http://my.opera.com | ブログ・モブログ・写真共有・友人交流・Opera Link/Unite |
| Myspace | http://www.myspace.com | 総合系 |
| myYearbook | http://www.myyearbook.com | 総合系・チャリティ |
| Nasza-klasa.pl | http://www.nk.pl | 学校・大学・友人（ポーランドで人気） |
| Netlog | http://www.netlog.com | 総合系（欧州・トルコ・アラブ世界・カナダのケベックで人気）旧 Facebox/Redbox |
| Nettby | http://www.nettby.no | ノルウェーのコミュニティ |
| Nexopia | http://www.nexopia.com | カナダ |
| NGO Post | http://www.ngopost.org | 非営利ニュース共有・ネットワーキング（主にインド） |
| Ning | http://www.ngopost.org | ユーザーが独自の SNS／サイトを作成 |
| Odnoklassniki | http://odnoklassniki.ru | 同級生と再会（ロシア・旧ソ連で人気） |
| OneClimate | http://www.oneclimate.net | 気候変動に関する非営利 SNS |
| OneWorldTV | http://tv.oneworld.net | 社会問題・開発・環境に関心のある人向け非営利動画共有／SNS |
| Open Diary | http://www.opendiary.com | 最初期のオンラインブログコミュニティ（1998年創設） |
| Orkut | http://orkut.com | 総合系（Google 所有、印・ブラジルで人気） |
| OUTeverywhere | http://www.outeverywhere.com | ゲイ／LGBTQ コミュニティ |
| Passportstamp | http://www.passportstamp.com | 旅行 |
| Partyflock | http://partyflock.nl | ハウス／EDM 好き向け蘭コミュニティ（国内最大級） |
| Picasa | http://picasa.google.com | （説明なし） |
| PicFog | http://picfog.com | Twitter の画像をリアルタイム表示 |
| Pingsta | http://www.pingsta.com | 世界のインターネットワーク専門家の協業プラットフォーム |
| Plaxo | http://www.plaxo.com | アグリゲーター |
| Playahead | http://www.playahead.se | スウェーデン／デンマークのティーン向け |
| Playlist.com | http://www.playlist.com | 総合・音楽 |
| Plurk | http://www.plurk.com | マイクロブログ／RSS／更新（台湾で非常に人気） |
| Present.ly | http://www.presently.com | 企業向け SNS／マイクロブログ |
| Qapacity | http://www.qapacity.com | 事業者向け SNS とビジネスディレクトリ |
| Quechup | http://quechup.com | 総合・友人・出会い |
| Qzone | http://qzone.qq.com | 総合系（簡体字中国語・中国本土向け） |
| Raptr | http://raptr.com | ビデオゲーム |
| Ravelry | http://www.ravelry.com | 編み物・かぎ針編み |
| Renren | http://renren.com | 中国の主要サイト |
| ResearchGate | http://researchgate.net | 研究者向け SNS |
| ReverbNation.com | http://www.reverbnation.com | ミュージシャン／バンド向け SNS |
| Ryze | http://www.ryze.com | ビジネス |
| ScienceStage | http://sciencestage.com | 科学志向のマルチメディアプラットフォーム＆ネットワーク |
| Scispace.net | http://scispace.net | 科学者のコラボレーションサイト |
| ShareTheMusic | http://www.sharethemusic.com | 音楽コミュニティ（無料・合法で共有／視聴） |
| Shelfari | http://www.shelfari.com | 本 |
| Skyrock | http://skyrock.com | 仏語圏の SNS |
| Social Life | http://www.sociallife.com.br | ブラジルのジェットセッターと世界のソーシャルエリート |
| SocialVibe | http://www.socialvibe.com | チャリティ向け SNS |
| Sonico.com | http://www.sonico.com | 総合（中南米・スペイン語／ポルトガル語圏で人気） |
| Stickam | http://www.stickam.com | ライブ動画配信＆チャット |
| StudiVZ | http://www.studivz.net | 大学生向け（独語圏）。姉妹サイトに schülerVZ／meinVZ |
| StumbleUpon | http://www.stumbleupon.com | 興味関心に合うサイトを「スタンブル」 |
| Tagged | http://www.tagged.com | 総合（メールマーケとプライバシーで物議） |
| Talkbiznow | http://www.talkbiznow.com | ビジネス・ネットワーキング |
| Taltopia | http://www.taltopia.com | オンライン芸術コミュニティ |
| Taringa! | http://www.taringa.net | 総合 |
| TeachStreet | http://www.teachstreet.com | 教育／学習／指導（400科目以上） |
| TravBuddy.com | http://www.travbuddy.com | 旅行 |
| Travellerspoint | http://www.travellerspoint.com | 旅行 |
| tribe.net | http://www.tribe.net | 総合 |
| Trombi.com | http://www.trombi.com | Classmates.com のフランス子会社 |
| Tuenti | http://www.tuenti.com | スペインの大学・高校向け SNS（スペインで非常に人気） |
| Tumblr | http://www.tumblr.com | 総合（マイクロブログ／RSS） |
| Twitter | http://twitter.com | 総合（マイクロブログ／RSS／更新） |
| twitpic | http://twitpic.com | （説明なし） |
| Vkontakte | http://vkontakte.ru/ | ロシア語圏最大の SNS（旧ソ連含む） |
| Vampirefreaks.com | http://www.vampirefreaks.com | ゴシック／インダストリアル系サブカルチャー |
| Viadeo | http://www.viadeo.com | グローバル SNS（英・仏・独・西・伊・葡対応）＆キャンパスネットワーキング |
| Virb | http://www.virb.com | アーティスト重視（ミュージシャン／写真家等） |
| Vox | http://www.vox.com | ブログ |
| Wakoopa | http://social.wakoopa.com | 新しいソフトやゲームを発見したいコンピュータ好き向け |
| Wattpad | http://www.wattpad.com | 読者と作者の交流＆電子書籍共有 |
| Wasabi | http://www.wasabi.com | 総合（英国拠点） |
| WAYN | http://www.wayn.com | 旅行とライフスタイル |
| WebBiographies | http://www.webbiographies.com | 家系・自伝 |
| WeeWorld | http://www.weeworld.com | 10〜17歳のティーン向け |
| WeOurFamily | http://www.weourfamily.com | プライバシーとセキュリティ重視の総合系 |
| Wer-kennt-wen | http://www.wer-kennt-wen.de | 総合 |
| weRead | http://weread.com | 本 |
| Windows Live Spaces | http://spaces.live.com | ブログ（旧 MSN Spaces） |
| WiserEarth | http://www.wiserearth.org | 社会的正義・環境運動のためのオンラインコミュニティ |
| Wordpress | http://wordpress.org | （説明なし） |
| WorldFriends | http://www.worldfriends.tv | （説明なし） |
| Xanga | http://www.xanga.com | ブログと「メトロ」エリア |
| XING | http://www.xing.com | ビジネス（主に欧州〔独・墺・瑞〕と中国） |
| Xt3 | http://www.xt3.com | カトリック向け SNS（WYD 2008 後に開設） |
| Yammer | http://www.yammer.com | オフィス同僚向け SNS |
| Yelp, Inc. | http://www.yelp.com | ローカルビジネスのレビューと掲示板 |
| Yfrog | http://yfrog.com | （説明なし） |
| Youmeo | http://youmeo.com | 英国の SNS（データポータビリティ重視） |
| Zoo.gr | http://www.zoo.gr | ギリシャのウェブ交流拠点 |
| Zooppa | http://zooppa.com | クリエイティブ人材のオンラインコミュニティ（ブランド主催の広告コンテスト） |

### 2.2.3　Cree.py
Cree.py（creepy）は、Twitter や Foursquare 等から **位置情報（geolocation）関連情報を自動収集**するベータツールです。flickr、twitpic、yfrog、plixi、twitrpix、shozu など多くの画像投稿サイトから位置情報を引き出せるため、位置特定に有効です。Cree.py は OSINT アプリケーションで、Debian 系ではリポジトリ追加→apt-get でインストール可能です（以下は要点のみ）。


例（Debian 系）
~~~~
echo "deb http://people.dsv.su.se/~kakavas/creepy/ binary/" >> /etc/apt/sources.list
apt-get update
apt-get install creepy
~~~~

Cree.py のインターフェースは、取得した位置情報をマップ上にプロットし、各投稿の文脈（どこから何が投稿されたか）を表示します。位置情報の突合やタイムライン把握に強力です。

---

## 2.3　インターネット上のフットプリント（Internet Footprint）
外部に公開されているターゲットのインフラや個人情報を収集・整理する工程です。後段の技術的攻撃やソーシャル攻撃に利用できるデータ（メールアドレス、サブドメイン、ユーザ名、公開ドキュメント等）を収集します。

### 2.3.1　メールアドレス（Email addresses）
メールアドレスの収集は一見些細ですが、命名規則（例：firstname.lastname@）、ターゲット候補、フィッシングやスプーフィング対象の特定に役立ちます。Maltego 等のツールで自動収集を行えます。

#### 2.3.1.1　Maltego
Paterva の Maltego は情報収集とフォレンジクスのためのツールで、データマイニングや視覚的マッピングに優れます。メールアドレス収集やサブドメインのマッピング、DNS/WHOIS/ソーシャルデータの変換（transform）を自動化できます。主要な操作の流れ（導入的）：

- 新規グラフを作成 → Palette から「Domain」エンティティをドラッグ → ターゲットのドメイン名を設定  
- ドメイン→「To Website DNS[using search engine]」などの transform を実行してサブドメインを収集  
- サブドメイン群を選択 → 「To IP Address [DNS]」で IP 解決 → 「To Netblock [Using natural boundaries]」でネットブロック特定  
- 以降、電話番号、メール、地理情報などの transform を順次実行していく

Community edition は transform 結果数に制限があります（例：12件）。全 transform を無差別に実行するとデータ過多になりがちなので、目的に合わせた絞り込みが重要です。

#### 2.3.1.2　TheHarvester
TheHarvester（Christian Martorella 作）は、検索エンジンや PGP キーサーバ等から **メールアカウントやサブドメイン名**を収集するシンプルかつ有効なツールです。使用例（コマンド概略）：

~~~~
root@pentest:/pentest/enumeration/theharvester# ./theHarvester.py

*************************************
*TheHarvester Ver. 1.6             *
*Coded by Christian Martorella      *
*Edge-Security Research             *
*cmartorella@edge-security.com      *
*************************************


Usage: theharvester options

       -d: domain to search or company name
       -b: data source (google,bing,pgp,linkedin)
       -s: start in result number X (default 0)
       -v: verify host name via dns resolution
       -l: limit the number of results to work with(bing goes from 50 to 50 results,
            google 100 to 100, and pgp does'nt use this option)

Examples:./theharvester.py -d microsoft.com -l 500 -b google
         ./theharvester.py -d microsoft.com -b pgp
         ./theharvester.py -d microsoft -l 200 -b linkedin
~~~~
TheHarvesterは指定されたデータソースを検索し、結果を返します。この結果は、後で使用するためにOSINTドキュメントに追加する必要があります。
~~~~
root@pentest:/pentest/enumeration/theharvester# ./theHarvester.py -d client.com -b google -l 500

*************************************
*TheHarvester Ver. 1.6             *
*Coded by Christian Martorella      *
*Edge-Security Research             *
*cmartorella@edge-security.com      *
************************************* 

Searching for client.com in google : 

====================================== 


Limit: 500 
Searching results: 0 
Searching results: 100 
Searching results: 200 
Searching results: 300 
Searching results: 400 

Accounts found: 
==================== 
admin@client.com 
nick@client.com 
jane@client.com 
sarah@client.com 
~~~~

#### 2.3.1.3　NetGlub
NetGlubはMaltegoに非常によく似たオープンソースツールです。NetGlubはデータマイニングおよび情報収集ツールであり、収集した情報を分かりやすい形式で表示します。現時点ではNetGlubのドキュメントは存在しないため、必要なデータを取得するための手順を記載します。
NetGlubのインストールは簡単ではありませんが、以下のコマンドを実行するだけで完了します。
~~~~
apt-get install build-essential mysql-server libmysqlclient-dev zlib1g-dev libperl-dev libnet-ip-perl libopenssl-ruby ruby-dev ruby omt php5-cli nmap libnet-dns-perl libnet-ip-perl python-dev
wget http://pypi.python.org/packages/source/s/simplejson/simplejson-2.1.5.tar.gz
tar -xzvf simplejson-2.1.5.tar.gz
cd simplejson-2.1.5
python2.7 setup.py build
python2.7 setup.py install 
cd ..
wget http://sourceforge.net/projects/pyxml/files/pyxml/0.8.4/PyXML-0.8.4.tar.gz
tar -xvzf PyXML-0.8.4.tar.gz
cd PyXML-0.8.4
wget http://launchpadlibrarian.net/31786748/0001-Patch-for-Python-2.6.patch
patch -p1 < 0001-Patch-for-Python-2.6.patch
python setup.py install 
cd /pentest/enumeration
~~~~
この時点では、QT-SDK の GUI インストールを使用します。ここで特に注意すべき点は、インストール中にインストールパスを /opt/qtsdk に変更する必要があることです。別のパスを使用する場合は、以下のスクリプト内のパスをその変更に合わせて更新する必要があります。
QT-SDK のインストール中に外部依存関係の確認が表示されるので、「apt-get install libglib2.0-dev libSM-dev libxrender-dev libfontconfig1-dev libxext-dev」を実行してください。
~~~~
wget http://blog.hynesim.org/ressources/install/qt-sdk-linux-x86-opensource-2010.03.bin
chmod +x qt-sdk-linux-x86-opensource-2010.03.bin
./qt-sdk-linux-x86-opensource-2010.03.bin
wget http://www.graphviz.org/pub/graphviz/stable/SOURCES/graphviz-2.26.3.tar.gz
tar -xzvf graphviz-2.26.3.tar.gz
cd graphviz-2.26.3
./configure
make
make install
cd /pentest/enumeration 
wget http://redmine.lab.diateam.net/attachments/download/1/netglub-1.0.tar.gz
tar -xzvf netglub-1.0.tar.gz 
mv netglub-1.0 netglub
cd /pentest/enumeration/netglub/qng/
/opt/qtsdk/qt/bin/qmake
make
~~~~
次にMySQLを起動し、netglubデータベースを作成します。
~~~~
start mysql 
mysql -u root -ptoor

create database netglub;
use netglub;
create user "netglub"@"localhost";
set password for "netglub"@"localhost" = password("netglub");
GRANT ALL ON netglub.* TO "netglub"@"localhost";
quit

mysql -u root -ptoor netglub < /pentest/enumeration/netglub/master/tools/sql/netglub.sql  

cd /opt/qtsdk/qt/src/plugins/sqldrivers/mysql/
/opt/qtsdk/qt/bin/qmake INCLUDEPATH+=/usr/include/mysql/
make
cp /opt/qtsdk/qt/src/plugins/sqldrivers/mysql/libqsqlmysql.so /opt/qtsdk/qt/plugins/sqldrivers/.
cd /pentest/enumeration/netglub/master
/opt/qtsdk/qt/bin/qmake
make
cd tools/
./install.sh
cd /pentest/enumeration/netglub/slave
/opt/qtsdk/qt/bin/qmake
make
cd tools/
./install.sh
wget http://sourceforge.net/projects/xmlrpc-c/files/Xmlrpc-c%20Super%20Stable/1.16.34/xmlrpc-c-1.16.34.tgz/download
tar -zxvf xmlrpc-c-1.16.34.tgz
cd xmlrpc-c-1.16.34
./configure
make 
make install
~~~~
NetGlub をインストールしたら、きっと実行してみたくなるはずです。実際には次の4ステップです。まず MySQL が動作していることを確認します。

~~~~
start mysql
~~~~

次に NetGlub Master を起動します。

~~~~
/pentest/enumeration/netglub/master/master
~~~~

続いて NetGlub Slave を起動します。

~~~~
/pentest/enumeration/netglub/slave/slave
~~~~

最後に NetGlub の GUI を起動します。

~~~~
/pentest/enumeration/netglub/qng/bin/unix-debug/netglub
~~~~

これでメインインターフェースが表示されるはずです。Maltego に慣れているなら、インターフェースはとても馴染みやすいでしょう。インターフェースの主要領域は、ツールバー、パレット（Palette）、グラフ（ビュー）領域、概要（overview）領域、詳細（details）領域、プロパティ（property）領域の6つです。

（スクリーンショット挿入）

使用可能（または有効化済み）のトランスフォーム一覧もあります。執筆時点では、約33個のトランスフォームが存在します。トランスフォームとは、特定サイトに対して実際の処理を行うスクリプトのことです。

（スクリーンショット挿入）

グラフ領域では、トランスフォームの実行や、マイニングビュー、ダイナミックビュー、エッジ加重ビュー、エンティティリストのいずれかでデータを表示できます。概要領域は、トランスフォームによって発見されたエンティティの「ミニマップ」を提供します。詳細領域では、エンティティの具体的な内容を掘り下げて確認できます。関係性や、情報がどのように生成されたかの詳細を閲覧することも可能です。プロパティ領域では、選択したエンティティに対して、トランスフォームの特性が結果で埋め込まれた形で表示されます。NetGlub の使用を開始するには、パレットからトランスフォームをドラッグ＆ドロップしてグラフ領域に置きます。既定ではダミーデータが入っています。選択したトランスフォーム内のエンティティを編集するには、プロパティビュー内の項目を編集します。

最初に、ドメインなどのインターネット基盤情報を特定する必要があります。そのために、Domain トランスフォームをグラフ領域にドラッグ＆ドロップします。クライアントに適したドメイン名となるよう、トランスフォームを編集してください。［Run All Transforms］をクリックすれば、初期段階で必要となるデータのほぼすべてを収集できます。

これらのエンティティから得られたデータは、追加情報の取得にも活用されます。グラフ領域では、その結果が以下のように可視化されます。

（スクリーンショット挿入）

エンティティを選択して追加のトランスフォームを実行すると、収集されるデータは拡張されます。まだ使っていないが収集したいデータに対応するトランスフォームがある場合は、それをグラフ領域へドラッグし、プロパティビューで適切に設定してください。

NetGlub を正しく機能させるために、いくつか入力が必要となる情報があります。たとえば、クエリ先の DNS サーバを指定する必要があります。さらに、Alchemy と Open Calais の API キーの提供を求められます。

Alchemy の API キーは次のページで取得します：  
http://www.alchemyapi.com/api/register.html

Open Calais の API キーは次のページで取得します：  
http://www.opencalais.com/APIkey

### 2.3.2　ユーザー名／ハンドル（Usernames/Handles）
メールアドレスと結び付くユーザ名やハンドルを特定すると、推測パスワード、個人の興味、別サービスでの同一ユーザの痕跡を得られます。ディスカッショングループ（ニュースグループ、フォーラム、メーリングリスト、チャット）を参照すると良いです。

### 2.3.3　ソーシャルネットワーク（Social Networks）
ユーザ名の存在確認やプロフィール調査は、160 を超える SNS を横断してチェックできるサービス等を利用して効率化できます。公開投稿、フレンド一覧、写真、いいね等から個人の行動パターンや関係性を把握します。

#### 2.3.3.1　ニュースグループ（Newsgroups）

- Google — http://www.google.com
- Yahoo Groups — http://groups.yahoo.com
- Delphi Forums — http://www.delphiforums.com
- Big Boards — http://www.big-boards.com


#### 2.3.3.2　メーリングリスト（Mailing Lists）

- TILE.Net — http://tile.net/lists
- Topica — http://lists.topica.com
- L-Soft Catalog (LISTSERV) — http://www.lsoft.com/lists/listref.html
- The Mail Archive — http://www.mail-archive.com


#### 2.3.3.3　チャットルーム（Chat Rooms）
- SearchIRC — http://searchirc.com
- Gogloom — http://www.gogloom.com


#### 2.3.3.4　フォーラム検索（Forums Search）

- BoardReader — http://boardreader.com
- Omgili — http://www.omgili.com

### 2.3.4　個人ドメイン名（Personal Domain Names）
ターゲット従業員が所有する個人ドメインは、ユーザ名や興味、職務外の公開情報（ブログ、ポートフォリオ）を提供します。ドメイン登録情報や Web コンテンツを調査してください。

### 2.3.5　個人の活動（Personal Activities）
個人が公開している音声・動画・ブログ・ポッドキャスト等は、趣味や居場所、タイムラインの手がかりになります。これらは攻撃の餌（pretext）作成やスケジューリングに役立つことがあります。

#### 2.3.5.1　音声（Audio）
- iTunes — http://www.apple.com/itunes
- Podcast.com — http://podcast.com
- Podcast Directory — http://www.podcastdirectory.com
- Yahoo! Audio Search — http://audio.search.yahoo.com

#### 2.3.5.2　動画（Video）
- YouTube — http://youtube.com
- Yahoo Video — http://video.search.yahoo.com
- Google Video — http://video.google.com
- Bing Video — http://www.bing.com/videos

### 2.3.6　アーカイブ情報（Archived Information）
元サイトの情報がすでに削除されている場合、アーカイブや検索エンジンのキャッシュから過去の情報を得られることがあります。例：

- Google キャッシュ検索: `cache:<site.com>`  
- Wayback Machine（Internet Archive）: http://www.archive.org

### 2.3.7　電子データ（Electronic Data）
偵察や情報収集に応答した電子データは、ターゲット企業や個人に関係する重要情報（日時、場所、言語、作成者など）を含むことがあります。ここでは特に公開ドキュメントやメタデータの抽出に注意します。

#### 2.3.7.1　ドキュメント漏えい（Document leakage）
公開ドキュメント（PDF、DOCX、XLS 等）を収集し、文書内に記載された日付・場所・担当者名等を抽出することで、内部手順や運用実態、従業員配置などの洞察が得られます。

#### 2.3.7.2　メタデータ漏えい（Metadata leakage）
メタデータの特定は、専門の検索エンジンを利用することで可能です。目的は、ターゲット企業に関連するデータを特定することです。  
ソーシャルネットワーキング上の投稿から、所在地、ハードウェア、ソフトウェア、その他の関連情報を特定できる場合もあります。  
メタデータ検索を行える代表的な検索エンジンは以下の通りです。

- **ixquick** — http://ixquick.com  
- **MetaCrawler** — http://metacrawler.com  
- **Dogpile** — http://www.dogpile.com  
- **Search.com** — http://www.search.com  
- **Jeffery’s Exif Viewer** — http://regex.info/exif.cgi  

これらの検索エンジンに加え、各種ドキュメントからファイルを収集し情報を抽出するためのツールも複数存在します。

##### 2.3.7.2.1　FOCA（Windows）
FOCA は多種のドキュメント／メディアフォーマットからメタデータを抽出するツールです。ユーザ名、パス、ソフトウェアバージョン、プリンタ情報、メールアドレスなどを引き出せます。

##### 2.3.7.2.2　Foundstone SiteDigger（Windows）
Foundstone の SiteDigger は GHDB（Google Hacking Database）や Foundstone DB のクエリを用いてドメイン上の情報を検索します。多数の事前定義クエリを使って追加情報を発見できます。

##### 2.3.7.2.3　Metagoofil（Linux/Windows）
Metagoofil はクライアントサイト上の公開ドキュメント（.pdf, .doc, .xls, .ppt 等）からメタデータを抽出し、HTML レポートと潜在的ユーザ名の一覧を生成します。抜粋されたファイルパスや MAC 情報等も抽出対象です。

コマンド例：
~~~~
metagoofil.py -d <client domain> -l 100 -f all -o <client domain>.html -t micro-files
~~~~

##### 2.3.7.2.4　Exif Reader（Windows）
Exif Reader は画像の EXIF 情報（シャッタースピード、フラッシュ、焦点距離等）を解析します。JPG 形式の画像から撮影情報を抽出できます。

##### 2.3.7.2.5　ExifTool（Windows/OS X）
ExifTool は多くのファイル形式に対応したメタ情報読み取りツールです。幅広いフォーマットのメタデータ抽出が可能です。

##### 2.3.7.2.6　画像検索（Image Search）
画像のトレースや類似画像検索には [TinEye](http://www.tineye.com/) 等が有用です。匿名プロフィールに掲載された写真の逆検索で、当該人物の別プロファイルや実名を突き止められる場合があります。

# 秘匿的収集（Covert gathering）

現地（オンサイト）での収集は、評価担当者がターゲットの物理的・環境的・運用上のセキュリティを観察・収集する機会を提供します。ここでは観察が主であり、可能な限り痕跡を残さずに施設の実態を把握することが目的です。

## 2.4　秘匿的収集（Covert gathering）

### 2.4.1　現地での収集（On-location gathering）
オンサイト訪問では、ターゲット周辺の環境や運用を直接観察できます。観察対象には周辺施設、物理的防護、従業員の挙動、廃棄物、無線周波数などが含まれます。

#### 2.4.1.1　隣接施設（Adjacent Facilities）
物理ロケーションを特定したら、**隣接施設**を洗い出して記録してください。隣接施設や共有サービス（共用駐車場、配送ヤード、電力・通信設備など）があれば可能な範囲で記載し、必要に応じて写真や地図で位置関係を残します。

#### 2.4.1.2　物理セキュリティ点検（Physical security inspections）
秘匿の物理セキュリティ点検は、ターゲットのセキュリティ姿勢を評価するために、秘密裏に行う観察活動です。観察が主要手段で、以下の項目を含めて（ただしこれに限定されない）記録します。

##### 2.4.1.2.1　警備員（Security guards）
警備員は視覚的抑止力として重要です。制服や巡回経路、詰所の位置、交代・休憩のパターン、武装の有無、通信手段（無線の使用）などを観察します。双眼鏡などを用いて安全な距離から動きを把握し、手順（受付対応、ゲートでの対応、入退室検査など）を特定します。武装している警備員を見つけた場合は事前にドキュメント化し、許可されていない限り近接行動や干渉は行わないでください。

##### 2.4.1.2.2　バッジの使用（Badge Usage）
バッジ使用は視覚的あるいは電子的アクセス制御手段です。バッジが常時見えるように着用されているか、出入口で提示が求められているか、バッジの形状・ホルダー・ID 表示の仕方、バッジの検証手順（セキュリティ担当による目視確認やカードリーダ）などを観察・記録します。可能ならバッジの外観（写真、但し法令・契約順守が必要）や読み取り方式の推定（磁気、近接、スマートカード等）を記録します。

##### 2.4.1.2.3　施錠装置（Locking devices）
施錠装置（機械式／電子式、デッドボルト、暗証式、暗証＋カード、シリンダ錠、シーケンス錠など）の種類と配置を観察します。扉が主に出入口用か非常口用かを判別し、カバーされている配線や外部ハウジングの有無、使用感（摩耗、補修跡）もメモします。可能であれば、写真や位置のスケッチを残します（法令・契約順守のもとで）。

##### 2.4.1.2.4　侵入検知システム（IDS）／アラーム（Intrusion detection systems (IDS)/Alarms）
侵入検知・アラームシステムの存在（ドア／窓開閉センサー、ガラス破砕センサー、振動センサー、マグネット接点、パッシブ赤外線 PIR、地域分割の有無）を観察します。観察対象には警備会社のステッカーや配線の痕跡、警告掲示、アラームボックス、サイレントアラームに関する標識などが含まれます。システムが物理的に保護されているかどうか（筐体の施錠、ケーブル保護）も記録します。

##### 2.4.1.2.5　防犯照明（Security lighting）
セキュリティ照明は、物理的な敷地における**予防的および是正的手段**として使用されることが多い。  
これは**侵入者の検知支援**、**侵入者への抑止効果**、または単に**安全性の向上（心理的安心感）**を目的とする場合もある。  
セキュリティ照明は、施設の環境設計（Crime Prevention Through Environmental Design：CPTED）の重要な構成要素である。

代表的なセキュリティ照明には、**投光照明（フラッドライト）**や**低圧ナトリウム灯**などがある。  
一晩中点灯させることを前提としたセキュリティ照明は、一般的に**高強度放電ランプ（HIDランプ）**の種類が使用される。  
その他の照明は**受動赤外線センサー（PIRセンサー）**などで制御され、**人や動物が近づいたときだけ点灯**する仕組みを採用している場合もある。

PIRセンサーによって動作する照明は、**即時点灯できる白熱灯**が使用されることが多い。  
これは常時点灯しないため、省エネ性よりも即応性が重視されるためである。  
PIRセンサーの作動は、侵入者に「検知された」と認識させることで**抑止効果**を高めると同時に、  
突然の明るさの変化によって人の注意を引くため、**検知効果**も高めることができる。  
一部のPIRユニットは、照明の点灯と同時に**チャイム音を鳴らす設定**も可能である。  
また、多くの現代的なユニットには**フォトセル（明暗センサー）**が組み込まれており、**暗いときのみ点灯**するよう制御されている。

物理的構造物の周囲に適切な照明を設置することは、侵入リスクを低減するために重要であるが、  
**照明の配置が不適切であると、むしろ監視の妨げになる**可能性があるため、注意深い設計が求められる。

さらに、セキュリティ照明は**破壊行為（バンダリズム）**の対象となる場合がある。  
侵入前に照明の効果を弱める目的で破損されるケースもあるため、  
照明は**高所に設置する**か、**ワイヤーメッシュ**や**ポリカーボネート製保護シールド**で防護する必要がある。  
また、照明器具を**外部から見えないように埋め込み式にする**、または**反射板（アルミまたはステンレス製）**を使用して  
光を導くといった設計も有効である。  

高セキュリティ環境では、**セキュリティ照明専用の予備電源（バックアップ電源）**を備える場合もある。  
監査や調査の際には、**使用されているセキュリティ照明の種類・数・設置位置を観察し、記録**しておくことが重要である。

##### 2.4.1.2.6　監視／CCTV システム（Surveillance /CCTV systems）
監視カメラ（察知用、抑止用）の存在、可視・不可視カメラの混在、パン・チルト・ズーム（PTZ）の有無、設置角度、保護の有無（防護ハウジング、金網、設置高さ）、配線・電源の露出状況、配信方式（有線／無線の推定）を確認します。カバー範囲の有無（死角）を推定し、物理的に損傷を受けやすいか（むき出しカメラ、手の届く位置）を記録します。ワイヤレスカメラの場合は周波数干渉やジamming の理論的リスクも留意します。

##### 2.4.1.2.7　入退室管理装置（Access control devices）
入退室管理装置（カードリーダ、PIN パッド、生体認証端末、インターホン等）の種類、設置場所、インターフェース（LCD、ボタン、カメラ・スピーカ付きか）、独立した知能化の有無（ローカルで判断するかコントローラ依存か）を観察します。代表的 reader の例（市場では見かける機種）や外観的特徴をメモします。設備が時刻打刻（出退勤）機能を兼ねているか、管理端末の設置箇所も確認します。

##### 2.4.1.2.8　環境設計（Environmental Design）
施設周辺の地形、植栽、建築デザイン、外構（フェンス、収納コンテナ、守衛ボックス、バリケード、メンテナンススペース）など、環境設計がセキュリティに与える影響を観察します。視界を遮る地形や植栽、見通しを悪くする構造物、ガードが見にくくなる死角などを特定し、隠密行動が可能かどうかの評価に組み込みます。

#### 2.4.1.3　従業員の挙動（Employee Behavior）
従業員の行動観察により、運用上の手順や暗黙知（許可されている運用、入退室の慣習、従業員の警戒レベル）を把握します。出入口の混雑時間、入退室の頻度、貴重物の移動方法、従業員の私物管理（ノートPC の持ち出し方等）を観察し、パターン化します。双眼鏡等を用い安全距離から観察してください。

#### 2.4.1.4　ダンプスターダイビング（廃棄物漁り）（Dumpster diving）
廃棄物（ゴミ箱・ダンプスター）から得られる情報は有益です。廃棄物には請求書、設計図のコピー、機器ラベル、社員名簿、付箋などが含まれる可能性があります。廃棄物漁りは私有地に踏み込む行為になる場合があるため、事前にエンゲージメントで許可されているか確認してください。法的に問題がある場合は**現場で廃棄物を持ち帰らず写真撮影のみ**とする等のルールを厳守します。

#### 2.4.1.5　RF／無線周波数スキャン（RF / Wireless Frequency scanning）
バンド（band）は、無線通信周波数スペクトラムの区分であり、同じ用途に使われるチャネルがまとめて割り当てられることが多い。干渉を防ぎ、無線スペクトラムを効率的に利用するために、類似のサービスは重複しない周波数範囲のバンドに割り当てられる。

慣例として、バンドは波長が 10^n メートル、あるいは周波数が 3×10^n ヘルツで区切られる。たとえば 30 MHz（10 m）は短波（短波より低く長波より長い領域）と VHF（より短波長で高周波）を分ける境界になる。これらは無線スペクトラムの区分であって、各国の周波数割当そのものとは異なる点に注意する。

各バンドには、干渉を避け、送受信機の互換性に関する取り決めを定める基本的なバンドプランがある。米国ではバンドプランは連邦通信委員会（FCC）が割当と管理を行っている。以下の表は、攻撃者が注目する可能性のある主なバンドを示す。

| バンド名 | 略称 | ITU バンド | 空中での周波数範囲（Hz）／波長 | 用途の例 |
|---:|:---:|:---:|:---|:---|
| 極超短波 | VHF | 8 | 30–300 MHz（10 m – 1 m） | FM、テレビ放送、地上⇄航空および航空間の視線通信、陸上移動体/海上移動体通信、アマチュア無線、気象ラジオ |
| 極超高周波 | UHF | 9 | 300–3000 MHz（1 m – 100 mm） | テレビ放送、電子レンジ、携帯電話、ワイヤレスLAN、Bluetooth、ZigBee、GPS、双方向無線（Land Mobile、FRS、GMRS 等）、アマチュア無線 |

RF サイトサーベイ（無線サイト調査、wireless site survey）は、特定の環境で使用されている周波数を特定するプロセスである。RF サイトサーベイを実施する際は、施設周辺の各地点での SNR（信号対雑音比）を測定し、有効な到達境界（effective range boundary）を特定することが非常に重要である。

作業を効率化するために、到着前に使用されている全周波数を可能な限り特定しておくべきである。特に注目すべきは警備員が使用している周波数や、対象がライセンスを保有する周波数帯である。以下のような情報源が、周波数・ライセンス情報の取得に役立つ。

| サイト | URL | 説明 |
|---|---|---|
| Radio Reference | http://www.radioreference.com/apps/db/ | 無料部分でも豊富な情報を提供するリソース |
| National Radio Data | http://www.nationalradiodata.com/ | FCC データベース検索（年間 $29） |
| Percon Corp | http://www.perconcorp.com | FCC データベース検索（有料・カスタム料金） |

最低限、検索エンジン（Google、Bing、Yahoo! など）を使って以下のような検索を実施すべきである：

- `"Target Company" scanner`  
- `"Target Company" frequency`  
- `"Target Company" guard frequency`  
- `"Target Company" MHz`  
- ターゲットに関する無線機器メーカーや再販業者のプレスリリース  
- ガード会社の契約に関するプレスリリース（ターゲット企業との契約情報）

これらの調査により、現地でのスキャンや装置配置、サーベイ計画を立てるための事前情報が得られる。

### 2.4.2　周波数の利用状況（Frequency Usage）
周波数カウンタは、繰り返し発生する電子信号の**1秒あたりの振動数（オシレーション）またはパルス数**を測定する電子機器である。周波数カウンタやスペクトラムアナライザを用いることで、対象施設周辺で使用されている**送信周波数**を特定することが可能である。一般的に観測される周波数帯の例は以下の通りである。

| バンド | 周波数帯域 |
|---|---:|
| VHF | 150 – 174 MHz |
| UHF | 420 – 425 MHz |
| UHF | 450 – 470 MHz |
| UHF | 851 – 866 MHz |
| VHF | 43.7 – 50 MHz |
| UHF | 902 – 928 MHz |
| UHF | 2400 – 2483.5 MHz |

スペクトラムアナライザは、使用されている周波数を**視覚的に表示**するために用いられる機器で、通常は周波数カウンタよりも特定の範囲に焦点を当てた解析が可能である。以下はスペクトラムアナライザの出力例で、使用中の周波数が明瞭に示されている。なお、このアナライザのスイープ範囲は **2399–2485 MHz** である。

（スクリーンショット挿入）

ターゲットの内外で使用されている**すべての周波数範囲を文書化**しておくことが重要である。


### 2.4.3　機器の識別（Equipment Identification）
現地調査（オンサイトサーベイ）の一環として、**使用されているすべての無線機器およびアンテナを特定**する必要がある。  
無線機については**メーカー名・型番**、アンテナについては**長さ・種類**も含めて確認する。  
以下は無線機器を特定する際に役立つ代表的な情報源である。

| サイト | URL | 説明 |
|---|---|---|
| HamRadio Outlet | http://www.hamradio.com | アマチュア無線に関する情報源として非常に有用 |
| BatLabs | http://www.batlabs.com | Motorola 製双方向無線システムの情報に関する優れた情報源 |

---

## 802.11 機器の特定

**802.11（Wi-Fi）機器**の特定は、視覚的に、または**RF（無線周波数）放射の分析**によって比較的容易に行うことができる。  
視覚的識別を行う場合、多くのベンダーでは自社ウェブサイトで製品情報を検索し、機器のメーカーおよびモデルを特定することが可能である。

| メーカー | URL |
|---|---|
| 3Com | http://www.3com.com |
| Apple | http://www.apple.com |
| Aruba | http://www.arubanetworks.com |
| Atheros | http://www.atheros.com/ |
| Belkin | http://www.belkin.com |
| Bluesocket | http://www.bluesocket.com/ |
| Buffalo Technology | http://www.buffalotech.com |
| Cisco | http://www.cisco.com |
| Colubris | http://www.colubris.com/ |
| D-Link | http://www.dlink.com |
| Engenius Tech | http://www.engeniustech.com |
| Enterasys | http://www.enterasys.com |
| Hewlett Packard | http://www.hp.com |
| Juniper | http://www.juniper.net |
| Marvell | http://www.marvell.com |
| Motorola | http://www.motorola.com |
| Netgear | http://www.netgear.com |
| Ruckus Wireless | http://www.ruckuswireless.com/ |
| SMC | http://www.smc.com |
| Trapeze | http://www.trapezenetworks.com/ |
| TRENDnet | http://www.trendnet.com |
| Versa Technology | http://www.versatek.com |

---

受動的な方法（パッシブ解析）でも、**RF 放射から得られるデータ**を基にメーカーを特定することが可能である。

---

## WLAN（無線LAN）の検出（Wireless Local Area Network Discovery）

**WLAN検出**とは、現在導入されている無線LANの種類を列挙（特定）することである。  
WLANの種類には、以下のものが含まれる：

- 暗号化されていない WLAN（Unencrypted WLAN）  
- WEP 暗号化 WLAN  
- WPA / WPA2 暗号化 WLAN  
- LEAP 暗号化 WLAN  
- 802.1x WLAN  

これらの情報を列挙・特定するためには、専用のツールが必要であり、その代表的なものについては次節で説明される。

#### 2.4.3.1　Airmon-ng
**Airmon-ng** は、無線インターフェース上で**モニターモード（Monitor Mode）を有効化**するためのツールである。  
また、必要に応じて**モニターモードからマネージドモード（Managed Mode）へ戻す**こともできる。

まず、使用している USB デバイスがシステムによって正しく認識されているかを確認する必要がある。  
そのためには、`lsusb` コマンドを使用して現在認識されている USB デバイスを一覧表示する。

~~~~
lsusb
~~~~

（スクリーンショット挿入）

上図の例では、ディストリビューションが **Prolific PL2303 Serial Port**（USB GPS 接続）および  
**Realtek RTL8187 Wireless Adapter** を正しく検出していることがわかる。  
これにより、システムがデバイスを認識していることが確認できたため、  
次に無線アダプタがすでにモニターモードになっているかを確認する。

パラメータを付けずに `airmon-ng` コマンドを実行すると、現在のインターフェース状態が表示される。

~~~~
airmon-ng
~~~~

（スクリーンショット挿入）

特定のインターフェースをモニターモードに設定するには、次のコマンドを実行する：

~~~~
airmon-ng start wlan0
~~~~

（スクリーンショット挿入）

既に `mon0` が存在する場合は、次のコマンドで一度停止してから再度モニターモードを有効化する。

~~~~
airmon-ng stop mon0
~~~~

再度パラメータなしで `airmon-ng` を実行することで、モニターモードの状態を確認できる。

（スクリーンショット挿入）

#### 2.4.3.2　Airodump-ng
Airodump-ng は Aircrack-ng スイートの一部であるネットワーク向けソフトウェアで、具体的には **パケットスニッファ** として動作し、空中を飛ぶトラフィックを Packet Capture（PCAP）ファイルや Initialization Vectors（IVS）ファイルへ保存し、無線ネットワークに関する情報を表示するツールである。

Airodump-ng は生の 802.11 フレームのパケットキャプチャに使われ、特に後で Aircrack-ng で利用するための **WEP の IV（Initialization Vectors）** の収集に適している。コンピュータに GPS レシーバを接続している場合、Airodump-ng は検出した AP（アクセスポイント）の座標をログに残すことができる。Airodump-ng を実行する前に、まず Airmon-ng スクリプトを起動して検出された無線インターフェースを確認しておくこと。

使用法（Usage）:

~~~~
airodump-ng <options> <interface> [, <interface>...]
~~~~

主なオプション（Options）:

- `--ivs`  
  : キャプチャした IV のみを保存する。

- `--gpsd`  
  : GPSd を利用する。

- `--write <prefix>`  
  : ダンプファイルのプレフィックスを指定する。

- `-w`  
  : `--write` と同義。

- `--beacons`  
  : ダンプファイルにすべてのビーコンを記録する。

- `--update <secs>`  
  : 画面の更新間隔（秒）を表示／設定する。

- `--showack`  
  : ack/cts/rts 統計を表示する。

- `-h`  
  : `--showack` 実行時に既知ステーションを非表示にする。

- `-f <msecs>`  
  : チャネルホッピング間の時間（ミリ秒）。

- `--berlin <secs>`  
  : パケットが一定時間受信されなくなった際に画面から AP/クライアントを除去するまでの時間（秒）。（既定: 120 秒）

- `-r <file>`  
  : 指定ファイルからパケットを読み込む。

- `-x <msecs>`  
  : アクティブスキャンのシミュレーション（ミリ秒）。

- `--output-format <formats>`  
  : 出力フォーマット。可能な値：`pcap`, `ivs`, `csv`, `gps`, `kismet`, `netxml`  
  短縮形式は `-o`。同オプションは複数回指定可能で、その場合それぞれの形式で出力される。`ivs` と `pcap` は同時に使えない。

補足：出力フォーマットは複数指定できるが、`ivs` と `pcap` は同時に使用不可。

Airodump-ng は検出した AP の一覧と、接続されているクライアント（"stations"）の一覧を画面に表示する。

（スクリーンショット挿入）

（別のスクリーンショット挿入）

画面の最上段の1行は、現在のチャネル、経過時間、現在日付、そしてオプションで WPA/WPA2 のハンドシェイクが検出されたかどうかを示す（検出されていればその情報が表示される）。

#### 2.4.3.3　Kismet-Newcore
Kismet-newcore は 802.11 無線 LAN 向けの**ネットワーク検出器、パケットスニファ、侵入検知システム**である。Kismet は raw monitor mode（モニターモード）をサポートする任意の無線カードで動作し、802.11a / 802.11b / 802.11g / 802.11n のトラフィックを捕獲できる。

Kismet はパッシブにパケットを収集して標準の名前付きネットワークを検出し、（時間をかけて）隠されたネットワークを検出・“露出”させたり、データトラフィックからビーコンを出さないネットワークの存在を推測したりすることでネットワークを識別する。

Kismet は以下の 3 つのコンポーネントで構成される：

- **Drones**：ワイヤレストラフィックをキャプチャしてサーバーに報告するキャプチャ要員。手動で起動する必要がある。  
- **Server**：Drone と接続しクライアント接続を受け付ける中央処理。サーバー自身もワイヤレストラフィックをキャプチャできる。  
- **Client**：サーバーに接続する GUI 部分。

Kismet を正しく動作させるには設定が必要である。まず、対象インターフェースが既にモニターモードになっているかを確認するには `airmon-ng` を実行する。

~~~~
airmon-ng
~~~~

（スクリーンショット挿入）

あるインターフェースをモニターモードに設定するには、`airmon-ng` を使って次のようにする：

~~~~
airmon-ng start wlan0
~~~~

（スクリーンショット挿入）

既に `mon0` が存在する場合は、先に停止しておく：

~~~~
airmon-ng stop mon0
~~~~

Airodump-ng と同様に、Kismet は複数インターフェースを利用できる。複数インターフェースを使う場合、`/etc/kismet/kismet.conf` を手動で編集する必要がある（`airmon-ng` は Kismet 用に複数インターフェースを自動設定できない）。各アダプタに対して `kismet.conf` に `source` 行を追加する。

注意：デフォルトでは、Kismet は起動したディレクトリにキャプチャファイルを保存する。これらのキャプチャは Aircrack-ng と併用できる。

コンソールで `kismet` と入力して Enter を押すと Kismet が起動する。

~~~~
kismet
~~~~

（スクリーンショット挿入）

前述のとおり Kismet は 3 つのコンポーネントで構成され、初期画面では**Kismet サーバーを起動する**か、**既に起動している別のサーバーを使用する**かを問われる。ここではローカルにサーバーを起動するため「Yes」を選択する想定で進める。

（スクリーンショット挿入）

Kismet はサーバー起動時に選択できるオプション群を提示する。

（スクリーンショット挿入）

`/etc/kismet/kismet.conf` にソースを事前設定していない場合は、パケットを取得するためのソースを指定する必要がある。

（スクリーンショット挿入）

前述のように、無線インターフェースからモニタ用のサブインターフェース（例：`mon0`）を作成している。ここでは例として `mon0` を指定する（実環境では名前が異なる場合がある）。

（スクリーンショット挿入）

Kismet のサーバーとクライアントが正常に起動すると無線ネットワークが表示され始める。例として WEP 有効のネットワークをハイライトしている。ソートオプションは多数あるためここで全機能を解説しないが、インターフェースに不慣れな場合は操作を試して慣れることを推奨する。

#### 2.4.3.4　inSSIDer
inSSIDer は Windows 環境で使いやすい Wi-Fi 発見／トラブルシューティングツールです。信号強度（dBi）を時系列で追跡し、GPS と連携して KML にエクスポートできるため Google Earth 上で可視化できます。Windows（XP/Vista/7 32/64bit）での互換性があり、GUI ベースでの調査に向きます。

---

### 補足
- オンサイトでの観察／収集は必ず**事前に顧客と合意したスコープと法的許可の範囲内**で行ってください。許可のない侵入や私有地への立ち入り、器物破損や電波妨害（ジャミング）などは法令違反となります。  
- 写真撮影や機器の接触を行う場合は、契約上の許可と評価上の必要性を確認し、記録は最小限に留めてください。  
- RF 機器の使用や測定は国・地域の法規制（例：周波数帯の利用規制）に従って実施してください。

# 外部フットプリンティング（External Footprinting）

外部フットプリンティングは、インテリジェンス収集の段階で、外部からターゲットへ直接問い合わせを行い得られた応答結果を収集するプロセスです。目的はターゲットに関する情報を可能な限り多く収集することにあります。

## 2.5　外部フットプリンティング（External Footprinting）

### 2.5.1　IP 範囲の特定（Identifying IP Ranges）
外部フットプリンティングでは、まずターゲットドメインの TLD を把握したうえで、どの WHOIS サーバが目的の情報を保持しているかを特定する必要があります。ターゲットドメインのレジストラ（Registrar）を突き止めることが出発点です。

WHOIS 情報は階層構造を持っています。ICANN（IANA）は全 TLD の権威的レジストリであり、手動 WHOIS クエリの出発点として有用です。

#### 2.5.1.1　WHOIS ルックアップ（WHOIS lookup）
代表的な参照先例：

- ICANN - http://www.icann.org
- IANA  - http://www.iana.com
- NRO   - http://www.nro.net
- AFRINIC - http://www.afrinic.net
- APNIC  - http://www.apnic.net
- ARIN   - http://ws.arin.net
- LACNIC - http://www.lacnic.net
- RIPE   - http://www.ripe.net

適切なレジストラに問い合わせることで、登録者（Registrant）情報を取得できます。WHOIS 情報を提供するサイトは多数ありますが、ドキュメントの正確性を担保するためには**該当する公式レジストラのみ**を用いるべきです。

（参照例）
- InterNIC - http://www.internic.net/

#### 2.5.1.2　BGP ルッキンググラス（BGP looking glasses）
BGP に参加するネットワークの ASN（Autonomous System Number）を特定することが可能です。BGP 経路は世界中へ広告されるため、BGP4 / BGP6 のルッキンググラスを使って経路情報を確認できます。

- BGP4 looking glasses - http://www.bgp4.as/looking-glasses
- BGP6 (he.net)       - http://lg.he.net/

### 2.5.2　アクティブ偵察（Active Reconnaissance）
アクティブ偵察は、ターゲットへ対して直接的な問い合わせや操作を行い応答を得ることで情報を収集する行為を指します。具体的なツールや手法は後述のアクティブ・フットプリンティングに含まれます。

### 2.5.3　パッシブ偵察（Passive Reconnaissance）
パッシブ偵察は、ターゲットに直接アクセスすることなく公開情報や第三者の情報源を収集して分析する手法です。Google ハッキング（Google dorks）等による索引検索やキャッシュ調査もここに含まれます。

（参考）
~~~~
- Google Hacking - http://www.exploit-db.com/google-dorks
~~~~

### 2.5.4　アクティブ・フットプリンティング（Active Footprinting）
アクティブ・フットプリンティングは、ターゲットへ直接問い合わせを行い、その応答を解析して情報を取得する段階です。以下に代表的な技術を示します。

#### 2.5.4.1　ゾーン転送（Zone Transfers）
DNS ゾーン転送（AXFR）は、DNS サーバ間で DNS データベースを複製するためのトランザクションです。完全転送（AXFR）と増分転送（IXFR）の 2 種類があります。ゾーン転送が許可されていると、DNS レコードの全容が取得できるため重大な情報漏洩となります。ゾーン転送を試みる際の代表的ツールは `host`、`dig`、`nmap` などです。

##### 2.5.4.1.1　host
~~~~
host <domain> <DNS server>
~~~~

##### 2.5.4.1.2　dig
~~~~
dig @server domain axfr
~~~~

#### 2.5.4.2　逆引き DNS（Reverse DNS）
逆引き DNS（PTR）を使うと、与えた IP アドレスからその IP に紐づくホスト名が得られる場合があります。PTR レコードが存在しないと名前は返りませんが、存在すれば組織内で使われている有効なサーバ名の手掛かりになります。複数 IP を試すことで追加の名前情報を得ることが可能です。

#### 2.5.4.3　DNS ブルート（DNS Bruting）
ターゲットドメインに関連する情報を収集したら、DNS へ総当たり的に問い合わせを行い（DNS ブルート）、知られていないホスト名やサブドメインを列挙します。最も深刻なミスコンフィギュレーションの一つは、インターネットユーザーに対してゾーン転送を許可することです。以下のツールが DNS 列挙に用いられます。

##### 2.5.4.3.1　Fierce2（Linux）
Fierce2 は DNS 列挙を行うツールで、ゾーン転送を試行し、失敗した場合はワードリストを用いてサブドメインを列挙します。使用例：

~~~~
fierce -dns <client domain> -prefix <wordlist>
~~~~

共通プレフィックスのワードリスト（例：common-tla.txt）を利用して列挙を行います（入手先は環境により異なる）。

##### 2.5.4.3.2　DNSEnum（Linux）
DNSEnum も Fierce2 の代替となる DNS 列挙ツールで、サブドメインの総当たり、逆引き、ネットワーク範囲の列挙、whois クエリ、Google スクレイピング等を行います。使用例：

~~~~
dnsenum -enum -f <wordlist> <client domain>
~~~~

同様に共通プレフィックスのワードリストを用いて列挙します。

##### 2.5.4.3.3　Dnsdict6（Linux）
Dnsdict6 は THC IPv6 Attack Toolkit に含まれる IPv6 向けの DNS 辞書ブルートフォーサーです。IPv6 ドメインと辞書ファイルを指定して実行します。

#### 2.5.4.4　ポートスキャン（Port Scanning）
ポートスキャンは、ターゲット上で開放されているサービスやポートを特定するための基本的な手法です。代表的ツールは Nmap です。

##### 2.5.4.4.1　Nmap（Windows/Linux）
Nmap（Network Mapper）はネットワーク監査／スキャンのデファクト標準です。Linux と Windows の両方で動作し、コマンドライン／GUI（Zenmap）版があります。ここでは主にコマンドラインオプションの概略を示します（抜粋）：

~~~~
Usage: nmap [Scan Type(s)] [Options] {target specification}

# 例：
nmap -A -PN -sU -sS -T2 -v -p 1-65535 <client ip range>/<CIDR> -oA NMap_FULL_<client ip range>
nmap -A -PN client.com -oA NMap_client
~~~~

主な機能の一部：
- ホスト探索（-sn, -Pn など）  
- スキャン手法（-sS, -sT, -sU, -sN など）  
- サービス/バージョン検出（-sV）  
- スクリプトスキャン（--script, -sC）  
- OS 検出（-O）  
- タイミング（-T0〜-T5）やパフォーマンス調整  
- ファイアウォール/IDS 回避（フラグメント、デコイ、ソース偽装等）  
- IPv6 サポート（-6 等）

注意点：大規模 IP 範囲（100 IP を超える等）をスキャンする場合はポート指定を行わない等、スキャン戦略を調整します。Nmap の IPv6 機能は限定的なオプションに留まる点に注意してください。

（実行例の出力や解説は、Nmap のマニュアルや実環境の出力に依存します。）

### 2.5.4.5　SNMP スイープ（SNMP Sweeps）
SNMP スイープは、対象となるネットワーク機器やホストから大量の情報を取得する手法です。SNMP はステートレスなデータグラムプロトコルであり、無効な community string には応答しない点や、UDP の性質上「応答無し」が複数の原因（到達不可、SNMP 未実行、無効 community、応答遅延）を意味することがあります。SNMP によりルーティング情報、インターフェース、システム情報等を得られるため、有効な community を発見できれば有益な情報源となります。

##### 2.5.4.5.1　SNMPEnum（Linux）
SNMPEnum は単一ホストへ SNMP リクエストを投げ、応答をログに記録する Perl ベースのスクリプトです。

### 2.5.4.6　SMTP バウンスバック（SMTP Bounce Back）
SMTP のバウンスバック（NDR: Non-Delivery Report/Receipt、DSN、NDN 等）は、送信先への配信失敗を報告する自動メールです。バウンスメッセージには SMTP サーバ情報（ソフトウェア名やバージョン）が含まれることがあり、SMTP サーバの指紋付けに利用できます。

### 2.5.4.7　バナーグラビング（Banner Grabbing）
バナーグラビングは、ネットワーク上のサービスが公開するバナー情報を取得して、アプリケーションや OS の種類・バージョンを推定する列挙技術です。主に HTTP（80）、FTP（21）、SMTP（25）などのサービスで行われます。代表的なツールは Telnet、nmap、netcat などです。

#### 2.5.4.7.1　HTTP
HTTP に対する簡易なリクエスト例（バナー確認のための破壊的でない呼び出し例）：

~~~~
JUNK / HTTP/1.0
HEAD / HTTP/9.3
OPTIONS / HTTP/1.0
HEAD / HTTP/1.0
~~~~

これらのレスポンスヘッダやサーバ応答により、サーバソフトウェアや設定の手掛かりを得ることができます。

---

### 補足
- 外部フットプリンティングはターゲットや契約の範囲を超えるアクション（無許可のスキャンや攻撃的な操作）を行う前に、必ず法的・契約的な承認を得てください。  
- 多くの調査ツールはネットワークに負荷を与える可能性があります。業務影響を最小化するために、スキャン時間・速度・ターゲット範囲を慎重に設定してください。  
- 収集した WHOIS・BGP・DNS・ポート情報はドキュメント化し、後続のリスク評価やレポート作成に活用してください。

# 内部フットプリンティング（Internal Footprinting）

内部フットプリンティングは、内部ネットワークの観点からターゲットへ直接相互作用を行い得られた応答結果を収集するフェーズです。目的はターゲットについて可能な限り多くの情報を収集することにあります。

## 2.6　内部フットプリンティング（Internal Footprinting）

### 2.6.1　アクティブ・フットプリンティング（Active Footprinting）
アクティブ・フットプリンティングは、ターゲットに対して直接問い合わせや操作を行い、その応答を解析して情報を取得する活動を指します。以下に代表的な手法とツールを示します。

#### 2.6.1.1　Ping スイープ（Ping Sweeps）
アクティブ・フットプリンティングは、生存しているホストの特定（ライブホスト検出）から始まることが多く、Ping スイープ（Ping スキャン）を行って応答するホストを洗い出します。

##### 2.6.1.1.1　Nmap（Windows/Linux）
`nmap`（Network Mapper）はネットワーク監査／スキャンのデファクト標準です。Linux と Windows 両対応でコマンドラインと GUI（Zenmap）があります。ここでは主要オプションの抜粋を示します。

主な使用例（抜粋）：
~~~~
Usage: nmap [Scan Type(s)] [Options] {target specification}

# ホスト探索（Ping スイープ）例
nmap -sn <client ip range>/<CIDR>   # Ping スキャン（ポートスキャン無し）
nmap -sn 10.25.0.0/24
~~~~

出力例（抜粋）：
~~~~
Nmap scan report for 10.25.0.1
Host is up (0.0030s latency).
MAC Address: C0:C1:C0:09:5C:16 (Unknown)
...
Nmap done: 256 IP addresses (4 hosts up) scanned in 6.19 seconds
~~~~

（その他のオプション・機能は Nmap マニュアルを参照）

##### 2.6.1.1.2　Alive6（Linux）
`Alive6`（THC IPv6 Attack Toolkit の一部）は、IPv6 ネットワーク上の生存ホストを検出するために効果的なツールです。インタフェースを指定して実行するとローカルリンクで稼働中の IPv6 システムを列挙できます。

### 2.6.1.2　ポートスキャン（Port Scanning）
ポートスキャンはホスト上で開放されているサービスを特定する基本的手法です。内部ではより詳細なスキャンやバージョン検出、スクリプトによる脆弱性検査を行う場合があります。

#### 2.6.1.2.1　Nmap（Windows/Linux）
Nmap のポートスキャン例（全 TCP/UDP ポート）：
~~~~
nmap -A -PN -sU -sS -T2 -v -p 1-65535 <client ip range>/<CIDR> -oA NMap_FULL_<client_ip_range>
nmap -A -PN -sU -sS -T2 -v -p 1-65535 client.com -oA NMap_FULL_client
~~~~

大規模 IP 範囲（例：100 IP 超）をスキャンする場合はポート範囲指定を省くなど戦略を調整します。IPv6 のスキャンでは一部オプションが限定されます（例：`-6`、`-sT`、`-sn`、`-sL`、バージョン検出など）。

### 2.6.1.3　SNMP スイープ（SNMP Sweeps）
SNMP スイープは、ネットワーク機器から多くの情報を得られるため有益です。SNMP はステートレス UDP ベースで、無効な community string では応答しない点、UDP の性質上「応答無し」が複数の原因を意味する点に留意してください。

#### 2.6.1.3.1　SNMPEnum（Linux）
`SNMPEnum` は対象ホストへ SNMP リクエストを送信し、返答を待ってログする Perl スクリプトです。ルーティング情報やインターフェース情報、システム詳細などを取得可能です（有効な community を必要とする）。

### 2.6.1.4　Metasploit
Metasploit フレームワークは、アクティブなフットプリンティングや脆弱性検証の一部に利用できます。実戦的な利用法や自動化されたモジュールについては Metasploit の学習リソース（例：Metasploit Unleashed）を参照してください。

### 2.6.1.5　ゾーン転送（Zone Transfers）
DNS ゾーン転送（AXFR/IXFR）は DNS サーバ間でゾーンデータを複製する仕組みで、誤設定により外部または内部からのゾーン転送で大量の DNS 情報が漏れる可能性があります。内部環境から `host` や `dig`、`nmap` 等で試行します。

#### 2.6.1.5.1　host
~~~~
host <domain> <DNS server>
~~~~

#### 2.6.1.5.2　dig
~~~~
dig @server domain axfr
~~~~

### 2.6.1.6　SMTP バウンスバック（SMTP Bounce Back）
SMTP のバウンスバック（NDR / DSN / NDN）は、配送失敗時に自動生成されるメッセージです。バウンスに含まれるヘッダ等から SMTP サーバのソフトウェアやバージョンを推測できる場合があり、サーバのフィンガープリントに利用できます。

### 2.6.1.7　逆引き DNS（Reverse DNS）
逆引き DNS（PTR）により IP からホスト名を得られる場合があります。PTR レコードが存在するか検証し、複数 IP を試すことで組織内で使用されている有効なサーバ名を収集できます。

### 2.6.1.8　バナーグラビング（Banner Grabbing）
バナーグラビングはサービスが返すバナー情報を取得してアプリケーションや OS 情報を推定する手法です。主に HTTP（80）、FTP（21）、SMTP（25）などで行い、`telnet`、`nmap`、`netcat` 等を使います。IPv6 向けには `netcat` の IPv6 対応版等を利用します。

#### 2.6.1.8.1　HTTP
HTTP に対する簡易リクエスト例（バナー確認）：
~~~~
JUNK / HTTP/1.0
HEAD / HTTP/9.3
OPTIONS / HTTP/1.0
HEAD / HTTP/1.0
~~~~

これらに対するレスポンスヘッダからサーバ情報や設定の手掛かりを得ます。

#### 2.6.1.8.2　httprint
`httprint` はウェブサーバのフィンガープリンティングツールで、サーババナーが偽装されている場合でも挙動／文字列シグネチャからサーバ種別を特定できます。無線機器やルータ等、サーババナーを持たないデバイスの検出にも有用で、シグネチャデータベースの拡張も容易です。

### 2.6.1.9　VoIP マッピング（VoIP mapping）
VoIP マッピングは、SIP 等のセッションプロトコルを用いる VoIP 環境に関するトポロジ（サーバ、PBX、ゲートウェイ、クライアント）やバージョン、機器種別を特定する活動です。SIP の基本（REGISTER, OPTIONS, INVITE）を理解していることが前提となります。

#### 2.6.1.9.1　内線（Extensions）
エクステンション（内線）とは SIP 接続を開始するクライアント（IP 電話、ソフトフォン等）です。`REGISTER` 等の応答により有効なユーザ名／内線を検出できます。

#### 2.6.1.9.2　Svwar
`Svwar`（sipvicious スイート）は内線の列挙を行うツールで、範囲指定や辞書ファイルを使った列挙が可能です。デフォルトの列挙手段は `REGISTER` を用いることが多いです。

#### 2.6.1.9.3　enumIAX
`enumIAX` は Asterisk の IAX2 プロトコル向けのユーザ名列挙ツールで、連続試行（Sequential）や辞書攻撃（Dictionary）モードをサポートします。Asterisk サーバ検出後のユーザ列挙に用いられます。

### 2.6.1.10　パッシブ偵察（Passive Reconnaissance）
アクティブな攻撃や発見を伴わない方法で情報を取得する手法です。内部ではパッシブなパケットキャプチャ（スニッフィング）により多くの情報を得られます。

#### 2.6.1.10.1　パケットスニッフィング（Packet Sniffing）
パケットスニッフィングは、ネットワーク上を流れるパケットを収集・解析して IP/MAC アドレス、プロトコル、認証情報等を抽出する技術です。検出されにくくステルス性が高いことが特徴で、十分なパケット数を収集・解析することで OS フィンガープリントや稼働サービスの特定、場合によっては平文ログイン情報やパスワードハッシュの窃取が可能です。Telnet や旧版 SNMP 等は平文で認証情報を送信するため特に危険です。

---

### 補足
- 内部環境での調査／列挙は、必ず顧客の合意したスコープと法的許可の元で実施してください。無断でのスニッフィングや認証情報収集、サービス妨害は法令違反となる可能性があります。  
- スキャンや列挙はネットワーク負荷や業務影響を与えることがあるため、時間帯・速度・ターゲットの制御を慎重に行ってください。  
- 収集した情報はログとして保管し、報告書作成や後続の評価（リスク分析、対策提案）に活用してください。
