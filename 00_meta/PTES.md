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

ペネトレーションテストで必要となるツールの選定は、実施する取り組み（エンゲージメント）の**種類**や**深さ**など、複数の要因に依存します。一般的には、期待される結果でペネトレーションテストを完了するために、以下のツールが**必須**です。

---

## オペレーティングシステム（Operating Systems）

ペネトレーションテストで使用するオペレーティングプラットフォームの選定は、ネットワークおよび関連システムのエクスプロイトに成功するかどうかを左右する**重要要素**です。そのため、**主要な 3 つのオペレーティングシステムを同時に使用できる能力**が求められます。これは**仮想化**なしには実現できません。

### MacOS X
MacOS X は BSD 系のオペレーティングシステムです。標準のコマンドシェル（sh、csh、bash など）や、ペネトレーションテスト時に使用できるネイティブのネットワークユーティリティ（telnet、ftp、rpcinfo、snmpwalk、host、dig など）を備えており、当方のペネトレーションテストツール群の**基盤となるホストシステム**として最適です。これは**ハードウェアプラットフォーム**でもあるため、特定ハードウェアの選定を非常に簡単にし、**すべてのツールが設計どおりに動作**することを保証します。

### VMware Workstation
VMware Workstation は、ワークステーション上で複数の OS インスタンスを容易に動かすための**絶対必須**ツールです。商用パッケージとして**正式サポート**があり、無償版にはない**暗号化機能**や**スナップショット機能**を提供します。VM 上で収集したデータを暗号化できなければ**機密情報が危険にさらされる**ため、**暗号化をサポートしない版は使用すべきではありません**。以下に挙げる OS は、VMware の**ゲスト**として実行します。

#### Linux
Linux は多くのセキュリティコンサルタントの**第一選択**です。プラットフォームとして**汎用性**が高く、カーネルは先端技術やプロトコルに対する**低レベルのサポート**を提供します。主流の **IP ベース攻撃／侵入ツール**は、概ね問題なく Linux 上でビルド・実行できます。こうした理由から、必要なツールをすべて備えた **BackTrack** がプラットフォームの第一選択となります。

#### Windows XP/7
**Windows XP/7** は、特定のツールを使用する上で**必要**です。商用ツールや Microsoft 由来のネットワーク評価・ペネトレーションツールの多くが、このプラットフォーム上で安定して動作します。

---

## 無線周波数ツール（Radio Frequency Tools）

### 周波数カウンタ（Frequency Counter）
**10 Hz～3 GHz** をカバーできる周波数カウンタが望ましい。手頃な価格帯の例として **MFJ-886 Frequency Counter** があります。

### 周波数スキャナ（Frequency Scanner）
スキャナは、2 つ以上の離散周波数を自動で**チューニング（走査）**し、いずれかで信号を検知すると停止し、最初の送信が終了すると他の周波数の走査を続ける**受信機**です。**フロリダ、ケンタッキー、ミネソタ**では、**FCC（連邦通信委員会）発行の現行アマチュア無線免許**を有する者以外は使用してはいけません。推奨ハードウェアは **Uniden BCD396T Bearcat Handheld Digital Scanner** または **PSR-800 GRE Digital trunking scanner** です。

### スペクトラムアナライザ（Spectrum Analyzer）
スペクトラムアナライザは、電気・音響・光の**波形のスペクトル構成**を解析する装置です。**無線送信機が連邦規定の標準どおり動作しているか**を確認し、**デジタル／アナログ信号の帯域幅**を直接観測により求めるために使用します。手頃な価格帯の例として **Kaltman Creations HF4060 RF Spectrum Analyzer** があります。

### 802.11 USB アダプタ（802.11 USB adapter）
802.11 USB アダプタは、**無線アダプタをテスト用システムに容易に接続**するためのものです。**認定済み以外の USB アダプタ**を用いると、必要な機能をすべて**サポートしない**場合があり問題となります。推奨ハードウェアは **Alfa AWUS051NH 500mW High Gain 802.11a/b/g/n high power Wireless USB** です。

### 外部アンテナ（External Antennas）
外部アンテナは**用途に応じて多様な形状**や**各種コネクタ**を持ちます。すべての外部アンテナは、Alfa と互換性のある **RP-SMA コネクタ**を備えている必要があります。Alfa には**無指向性（Omni）アンテナ**が同梱されるため、別途**指向性アンテナ**を用意します。**パネルアンテナ**が最適で、必要な性能を満たしつつ**携帯性**にも優れます。推奨ハードウェアは **L-com 2.4 GHz 14 dBi Flat Panel Antenna（RP-SMA）**。また、**L-com 2.4 GHz/900 MHz 3 dBi Omni Magnetic Mount Antenna（RP-SMA プラグ）** のような**マグネットマウントの無指向性アンテナ**も有用です。

### USB GPS
**RF 評価を適切に行うには GPS が必須**です。これがないと、**RF 信号がどこまで・どのように伝搬しているか**を把握できません。多数の選択肢がありますが、使用する **OS（Linux／Windows／Mac OS X）でサポートされる USB GPS** を入手してください。

---

## ソフトウェア（Software）

ソフトウェアの要件は**エンゲージメントのスコープ**に依存しますが、**完全なペネトレーションテスト**を適切に実施するために必要となり得る**商用およびオープンソース**のソフトウェアを以下に挙げます。

> **注記**：「Windows Only」は該当項目が **Windows 専用**であることを示します（原文中の `*` 記号）。

| Software | URL | 説明 | Windows Only |
|---|---|---|---|
| Maltego | http://www.paterva.com/web5 | 個人や企業に関するデータマイニングの**事実上の標準**。無償のコミュニティ版と有償版あり。 |  |
| Nessus | http://tenable.com/products/nessus | 有償／無償版のある**脆弱性スキャナ**。主に**内部ネットワーク**からの脆弱性発見と文書化に有用。 |  |
| IBM AppScan | http://www-01.ibm.com/software/awdtools/appscan | IBM の**自動 Web アプリケーションセキュリティテスト**スイート。 | * |
| eEye Retina | http://www.eeye.com/Products/Retina.aspx | 単一の Web ベースコンソールから管理できる**自動ネットワーク脆弱性スキャナ**。**Metasploit** と連携し、該当エクスプロイトがあれば Retina から直接起動して**脆弱性の実在**を検証可能。 |  |
| Nexpose | http://www.rapid7.com | **Metasploit** と同社の**脆弱性スキャナ**。無償／有償版あり、サポートや機能に差。 |  |
| OpenVAS | http://www.openvas.org | **Nessus** のフォークとして始まった**脆弱性スキャナ**。**毎日更新**される NVT（Network Vulnerability Tests）フィードを同梱（**20,000 超／2011 年 1 月時点**）。 |  |
| HP WebInspect | https://www.fortify.com/products/web_inspect.html | 複雑な Web アプリケーションを対象とした**Web アプリ脆弱性テスト**。JavaScript、Flash、Silverlight などをサポート。 | * |
| HP SWFScan | https://h30406.www3.hp.com/campaigns/2009/wwcampaign/1-5TUVE/index.php?key=swf | HP Web Security Research Group 開発の**無償ツール**。Flash アプリの脆弱性を自動検出。**デコンパイル**や**ハードコード資格情報**の発見にも有用。 | * |
| Backtrack Linux | [1] | もっとも充実した**ペネトレーションテスト向け Linux ディストリ**の一つ。多くの無償ペンテストツールを同梱し、**Ubuntu ベース**で拡張性が高い。**Live CD／USB／VM／HDD インストール**可。 |  |
| SamuraiWTF (Web Testing Framework) | http://samurai.inguardians.com | **Web アプリスキャン専用**の Live Linux ディストリ。Fierce、Maltego、WebScarab、BeEF など Web テスト特化ツールを同梱。 |  |
| SiteDigger | http://www.mcafee.com/us/downloads/free-tools/sitedigger.aspx | **Windows 用**の無償ツール（v3.0）。Google キャッシュを検索し、**脆弱性／エラー／設定不備／機密情報**などをサイト上から見つける。 | * |
| FOCA | http://www.informatica64.com/DownloadFOCA | Web サイトが公開する文書の**メタデータ解析**などを通じ、対象に関する情報を収集するツール。 | * |
| THC IPv6 Attack Toolkit | http://www.thc.org/thc-ipv6 | **IPv6／ICMPv6** の脆弱性を突くための、最大規模の**ツール集**。 |  |
| THC Hydra | http://thc.org/thc-hydra/ | 多様なサービスやリソースに対し高速に**総当たりログオン**を行うクラックツール。 | * |
| Cain | http://www.oxid.it/cain.html | **Windows** で動作するパスワードリカバリツール。ネットワークスニッフィング、辞書／総当たり／解析による**パスワード解読**、VoIP 録音、無線鍵回収、パスワードボックスの表示、キャッシュパスワードの取得、ルーティングプロトコル解析などを提供。 | * |
| cree.py | http://ilektrojohn.github.com/creepy/ | SNS や画像ホスティングから**位置情報関連データ**を収集し、**地図上に可視化**して文脈情報とともに提示。 |  |
| inSSIDer | http://www.metageek.net/products/inssider | **Windows 用 GUI** の Wi-Fi **検出／トラブルシュート**ツール。 | * |
| Kismet Newcore | http://www.kismetwireless.net | 802.11 の **L2 無線ネットワーク検出・スニッファ・IDS**。**モニタモード**対応アダプタで、SSID 公開／非公開を問わず受動収集。 |  |
| Rainbow Crack | http://project-rainbowcrack.com | 事前計算済みの**レインボーテーブル**を用いて各種ハッシュに対し**高速照合**を行うパスワードクラックツール。 |  |
| dnsenum | http://code.google.com/p/dnsenum | whois の**超強化版**のようなツール。DNS レコードの網羅的発見に加え、Google 経由の**サブドメイン発見**、**BIND バージョン**の特定なども試みる。 |  |
| dnsmap | http://code.google.com/p/dnsmap | **受動型**の DNS マッパー。**サブドメインの総当たり発見**に使用。 |  |
| dnsrecon | http://www.darkoperator.com/tools-and-scripts/ | Ruby 製の **DNS 列挙**スクリプト。TLD 展開、SRV レコード列挙、ホスト／サブドメイン総当たり、ゾーン転送、逆引き、一般的なレコードの特定に対応。 |  |
| dnstracer | http://www.mavetju.org/unix/dnstracer.php | 指定した DNS サーバが**どこから情報を得ているか**を特定し、**権威サーバ**へと至る連鎖を追跡。 |  |
| dnswalk | http://sourceforge.net/projects/dnswalk | **DNS デバッガ**。指定ドメインのゾーン転送を行い、**整合性や正確さ**を多面的に検査。 |  |
| Fierce | http://ha.ckers.org/fierce | ネットワークの**非連続な IP 範囲**を発見する**ドメインスキャン**。 |  |
| Fierce2 | http://trac.assembla.com/fierce/ | 新たな開発チームにより保守される **Fierce の更新版**。 |  |
| FindDomains | http://code.google.com/p/finddomains | 多数の IP に分散する**ドメイン／サイト／バーチャルホスト**の発見に有用な**マルチスレッド**検索ツール。コンソール UI を備え、**自動化**への組み込みが容易。 | * |
| HostMap | http://hostmap.lonerunners.net | 指定 IP アドレス上の**すべてのホスト名／バーチャルホスト**を自動発見する**無償ツール**。 |  |
| URLcrazy | http://www.morningstarsecurity.com/research/urlcrazy | **タイポドメイン**生成ツール。対象企業に関連する**ドメイン占有（スコッティング）**の発見や、独自の生成にも役立つ。 |  |
| theHarvester | http://www.edge-security.com/theHarvester.php | 検索エンジンや **PGP キーサーバ**など**公開ソース**から、**メール／ユーザ名／ホスト名・サブドメイン**を収集。 |  |
| The Metasploit Framework | http://metasploit.com | あらゆるプラットフォーム向けの**リモートエクスプロイト**と**ポストエクスプロイト**の**巨大コレクション**。**ほぼ毎日**機能・エクスプロイトが追加されるため、常に **svn update** すること。詳細は書籍（http://nostarch.com/metasploit.htm）を参照。 |  |
| The Social-Engineer Toolkit (SET) | http://www.secmaniac.com/download/ | **人的要素**を狙う高度な攻撃に特化。**正規サイトに似せたダミーサイト**や**悪性メール**を生成し、**ソーシャルエンジニアリング攻撃**を補完。 |  |
| Fast-Track | http://www.secmaniac.com/download/ | **自動化ペンテスト**ツールスイート。多くの攻撃は、Web アプリにおける**クライアントサイド入力の不適切なサニタイズ**、**パッチ管理不備**、**ハードニング不足**に起因。Linux 上で動作し、**Metasploit 3** に依存。 |  |

> [1] 原文では BackTrack の参照リンクが `[1]` として記されているのみです。
