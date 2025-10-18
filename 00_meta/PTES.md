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

<<<BEGIN>>>
# 2 情報収集（Intelligence Gathering）

情報収集（Intelligence Gathering）は、評価の行動を導くためにデータ、すなわち「インテリジェンス」を収集するフェーズです。広義にはこのインテリジェンス収集は従業員、施設、製品、計画に関する情報を含みます。より広い観点では、競合他社の潜在的に機密・私的な「インテリジェンス」や、ターゲットに関連するその他の情報が含まれることがあります。

## 2.1 OSINT

オープンソースインテリジェンス（OSINT）とは、単純に言えば公に（オープンに）利用可能な情報源を**発見し解析すること**です。ここでの重要な要素は、このインテリジェンス収集プロセスの目的が、攻撃者や競合にとって価値のある**最新かつ関連性のある情報を生成すること**にある点です。大半の場合、OSINTは単に様々なソースでウェブ検索を行う以上のものです。

### 2.1.1 企業（Corporate）

特定のターゲットに関する情報には、その**法的実体（legal entity）**に関する情報を含めるべきです。米国の多くの州では、法人（Corporations）、有限責任会社（limited liability companies）および有限組合（limited partnerships）は州の担当部署に登記を行うことが要求されています。この部署は登記の保管者として機能し、書類・登記のコピーや認証を保持します。これらの情報には、株主、メンバー、役員、その他ターゲット実体に関与する人物に関する情報が含まれている可能性があります。

#### 州（State） — 参照 URL
（原文どおり各州と URL を列挙）

Alabama  
http://sos.alabama.gov/BusinessServices/NameRegistration.aspx

Alaska  
http://www.dced.state.ak.us/bsc/corps.htm

Arizona  
http://starpas.azcc.gov/scripts/cgiip.exe/WService=wsbroker1/main.p

Arkansas  
http://www.sosweb.state.ar.us/corps/incorp

California  
http://kepler.sos.ca.gov/

Colorado  
http://www.state.co.us

Connecticut  
http://www.state.ct.us

Delaware  
http://www.state.de.us

District of Columbia  
http://www.ci.washington.dc.us

Florida  
http://www.sunbiz.org/search.html

Georgia  
http://corp.sos.state.ga.us/corp/soskb/CSearch.asp

Hawaii  
http://www.state.hi.us

Idaho  
http://www.accessidaho.org/public/sos/corp/search.html?SearchFormstep=crit

Illinois  
http://www.ilsos.gov/corporatellc

Indiana  
http://secure.in.gov/sos/bus_service/online_corps/default.asp

Iowa  
http://www.state.ia.us

Kansas  
http://www.accesskansas.org/apps/corporations.html

Kentucky  
http://ukcc.uky.edu/~vitalrec

Louisiana  
http://www.sec.state.la.us/crpinq.htm

Maine  
http://www.state.me.us/sos/cec/corp/ucc.htm

Maryland  
http://sdatcert3.resiusa.org/ucc-charter

Massachusetts  
http://ucc.sec.state.ma.us/psearch/default.asp

Michigan  
http://www.cis.state.mi.us/bcs_corp/sr_corp.asp

Minnesota  
http://www.state.mn.us/

Mississippi  
http://www.sos.state.ms.us/busserv/corpsnap

Missouri  
http://www.state.mo.us

Montana  
http://sos.state.mt.us

Nebraska  
http://www.sos.state.ne.us/htm/UCCmenu.htm

Nevada  
http://sandgate.co.clark.nv.us:8498/cicsRecorder/ornu.htm

New Hampshire  
http://www.state.nh.us

New Jersey  
http://www.state.nj.us/treasury/revenue/searchucc.htm

New Mexico  
http://www.sos.state.nm.us/UCC/UCCSRCH.HTM

New York  
http://wdb.dos.state.ny.us/corp_public/corp_wdb.corp_search_inputs.show

North Carolina  
http://www.secstate.state.nc.us/research.htm

North Dakota  
http://www.state.nd.us/sec

Ohio  
http://serform.sos.state.oh.us/pls/report/report.home

Oklahoma  
http://www.oklahomacounty.org/coclerk/ucc/default.asp

Oregon  
http://egov.sos.state.or.us/br/pkg_web_name_srch_inq.login

Pennsylvania  
http://www.dos.state.pa.us/DOS/site/default.asp

Rhode Island  
http://155.212.254.78

South Carolina  
http://www.scsos.com/corp_search.htm

South Dakota  
http://www.state.sd.us

Tennessee  
http://www.state.tn.us/sos/service.htm

Texas  
https://ourcpa.cpa.state.tx.us/coa/Index.html

Utah  
http://www.commerce.state.ut.us

Vermont  
http://www.sec.state.vt.us/seek/database.htm

Virginia  
http://www.state.va.us

Washington  
http://www.dol.wa.gov/business/UCC/

West Virginia  
http://www.wvsos.com/wvcorporations

Wisconsin  
http://www.wdfi.org/corporations/crispix

Wyoming  
http://soswy.state.wy.us/Corp_Search_Main.asp

### 2.1.2 物理（Physical）

OSINT の最初のステップは、対象企業の**物理的所在地**を特定することが多いです。この情報は公開されている（周知の）場所については入手しやすいですが、より秘匿性の高い施設については容易ではありません。公開されているサイトは、次のような検索エンジンで所在地を特定できることがよくあります：

- Google — http://www.google.com  
- Yahoo — http://yahoo.com  
- Bing — http://www.bing.com  
- Ask.com — http://ask.com

#### Locations（所在地）

##### Shared/Individual（共同／個別）
物理的な場所を特定する際、その場所が**独立した建物**なのか、単により大きな施設内の**スイート（区画）**なのかを確認することが重要です。隣接する事業所や共用部分も特定するよう努めてください。

##### Owner（所有者）
物理的な所在地が特定されたら、実際の**不動産所有者**を特定することが有用です。所有者は個人、グループ、法人のいずれかであり得ます。ターゲット企業が不動産を所有していない場合、物理的な改良や改善に制約があることがあります。

##### Land/tax records（土地・税記録）
Tax records: http://www.naco.org/Counties/Pages/CitySearch.aspx

土地や税の記録には、所有権、占有者、抵当会社、差押通知、写真など、ターゲットに関する豊富な情報が含まれることが一般的です。記録される情報や公開度合いは管轄によって大きく異なります。米国内では土地・税記録は通常郡（county）レベルで扱われます。

開始手順として、ターゲットの市または郵便番号が分かっている場合は、まず http://publicrecords.netronline.com/ のようなサイトでどの郡に属するかを特定します。その後 Google に切り替え、"XXXX county tax records"、"XXXX county recording office"、"XXXX county assessor" のようなクエリを使うと、該当する検索可能なオンラインデータベースがあれば辿り着けるはずです。存在しない場合でも、郡の登記所に電話して、特定の記録をファックスしてもらえるか尋ねることは可能です。

##### Building department（建築部門）
一部の評価では、さらに踏み込んで**地域の建築部門**に問い合わせることが有益な場合があります。市によってはターゲットが郡の管轄か市の管轄かが異なるため、どちらに問い合わせるべきかは電話で確認できます。

建築部門は通常、平面図、過去および現在の許可証、テナント改善情報などを保管しています。その情報の中には請負業者、技術者、建築家の名前などが埋もれていることがあり、これらは SET（Social-Engineer Toolkit）などと組み合わせて利用できます。多くの場合これらの情報を入手するには電話が必要になりますが、多くの建築部門は問い合わせに対して情報を提供してくれます。

床面図を入手するための口実の例としては、あなたが改装や増築を設計するために雇われた**建築コンサルタント**であり、元の図面のコピーがあればプロセスが円滑に進む、と伝える方法があります。

### 2.1.3 データセンターの所在（Datacenter Locations）

コーポレートサイト、公的届出、土地記録、検索エンジンなどを通じてターゲット企業の**データセンター所在地**を特定することは、追加の潜在的ターゲットを提供する可能性があります。

#### 2.1.3.1 タイムゾーン（Time zones）
ターゲットが活動するタイムゾーンを特定することは営業時間に関する有用な情報を提供します。また、ターゲットのタイムゾーンと評価チームのタイムゾーンの関係を理解することも重要です。テストを行う際にはタイムゾーン地図が参考になります。

（原文：TimeZone Map）

#### 2.1.3.2 オフサイトの集まり（Offsite gathering）
コーポレートサイトや検索エンジンを通じて、最近や今後の**オフサイトイベントやパーティ**を特定することは企業文化に関する有益な洞察を与えます。企業は従業員だけでなくビジネスパートナーや顧客向けにもオフサイト集まりを開催することが一般的であり、これらのデータは攻撃者にとって興味深い手掛かりとなり得ます。

#### 2.1.3.3 製品／サービス（Product/Services）
コーポレートサイト、ニュースリリース、検索エンジンを用いて、ターゲット企業の**製品やそのローンチに関連する重要なデータ**を特定することは、組織内部の動きに関する有用な洞察を提供します。企業は注目を集めたり既存および新規顧客へ通知するためにこうした情報を公開することが一般的です。公開情報には外国語文書、ラジオやテレビ放送、インターネットサイト、公開講演などが含まれます。

#### 2.1.3.4 会社の重要日（Company Dates）
重要な会社の日付は、スタッフの警戒レベルが通常より高まる可能性のある日を示すことがあります（取締役会、投資家会合、創立記念など）。一般に、祝日を観察する企業では人員が大幅に減るため、その期間に攻撃を試みるのは困難になり得ます。

#### 2.1.3.5 役職の特定（Position identification）
ターゲット組織の上位ポジションを特定し記録することは重要です。これは最終的な報告が正しい対象に向けられることを確保するために必要です。最低限、主要な従業員はエンゲージメントの一部として特定されるべきです。

#### 2.1.3.6 組織図（Organizational Chart）
組織構造を理解することは、その深さだけでなく広がりを把握するのに重要です。非常に大きな組織では新規スタッフや人員変動が見落とされることがありますが、小規模組織ではその可能性は低くなります。構造を把握することで機能グループに関する洞察を得られ、内部ターゲット選定に役立ちます。

#### 2.1.3.7 コーポレートコミュニケーション（Corporate Communications）
コーポレートサイトや求人エンジンを通じて企業のコミュニケーションを特定することで、ターゲット内部の運用に関する有益な洞察が得られます。

##### マーケティング（Marketing）
マーケティングコミュニケーションは、現在または将来の製品リリースや提携に関する企業発表にしばしば使用されます。

##### 訴訟（Lawsuits）
訴訟に関するコミュニケーションは、潜在的な脅威主体や注目すべきデータに関する手掛かりを与えることがあります。

##### 取引（Transactions）
企業取引に関するコミュニケーションは、マーケティング発表や訴訟に対する間接的な反応であることがあります。

#### 2.1.3.8 求人（Job openings）
コーポレートサイトや求人エンジンで公開されている現在の求人を検索することは、社内の動向を把握する上で有用です。企業は新たに導入する技術や将来の技術計画について求人情報に記載することがよくあり、これらは攻撃者にとって興味深い情報源となります。参照可能な求人検索サイトの例：

- Monster — http://www.monster.com  
- CareerBuilder — http://www.careerbuilder.com  
- Computerjobs.com — http://www.computerjobs.com  
- Craigslist — http://www.craigslist.org/about/sites

### 2.1.4 関係性（Relationships）

ターゲットの論理的な関係（ベンダー、ビジネスパートナー、法律事務所等）を特定することは、企業の運用を理解する上で重要です。公開情報（ニュースリリース、コーポレートサイト、業界フォーラム等）を活用してください。

#### Charity Affiliations（慈善団体との関係）
ターゲット企業の慈善団体関係を特定することは、企業文化や内部事情に関する洞察を与える可能性があります。企業がどの団体に寄付しているかを収集することで、攻撃者にとって関心ある情報が得られることがあります。

#### Network Providers（ネットワークプロバイダ）
割り当てられたネットブロックやアドレス情報、コーポレートサイト、検索エンジンを通じてネットワークのプロビジョニングやプロバイダを特定することは、ターゲットの潜在能力に関する重要な洞察を提供します。

#### Business Partners（ビジネスパートナー）
ビジネスパートナーを特定することは企業文化だけでなく利用中の技術スタックに関する手掛かりを得るうえで重要です。企業は提携契約を公表することが多く、これらの情報は攻撃者にとって有用です。

#### Competitors（競合）
競合を特定することは潜在的な敵対者を把握する窓口となります。競合は新規採用、製品ローンチ、提携などを発表し、これらがターゲットに影響を与えることがあるため、情報収集が重要です。

---

## 2.2 個人（Individuals）

### 2.2.1 ソーシャルネットワーキング・プロフィール（Social Networking Profile）

多数のアクティブなソーシャルネットワーキングサイトおよび大量のユーザが存在することから、従業員の友情、親族、共通の興味、金銭のやり取り、好み／嫌い、性的関係、信条などを特定するのに最適な場所です。従業員の社内知識や社内での評価が推察できることさえあります。

### 2.2.2 ソーシャルネットワーキング・サイト（Social Networking Websites）

（原文は多数のサイト名、URL、フォーカスを列挙しています。ここでは原文に忠実に列挙します）

例：  
Academia.edu — http://www.academia.edu （学術／研究者）  
Advogato — http://www.advogato.org （オープンソース開発者）  
aNobii — http://www.anobii.com/anobii_home （書籍）  
…（以降、原文どおりの長いリストが続きます）…

### Tone and Frequency（投稿のトーンと頻度）
従業員の投稿のトーンや頻度を把握することは、不満を持つ従業員や社内でのソーシャルネットワーキングの受容度を示す指標になり得ます。時間はかかりますが、従業員の勤務スケジュールや休暇期間を推定することも可能です。

### Location awareness（位置情報の把握）
多くのソーシャルネットワーキングサイトは投稿にジオロケーション情報を含める機能を提供しています。これにより、投稿時にその人物がどこにいたかを正確に特定することができます。また、アップロードされた画像にジオタグが含まれている場合もあります。ユーザがこの機能をオフにしている可能性はありますが、投稿本文に所在地を明示する場合もあります。

### 2.2.3 Cree.py

Cree.py は Twitter および FourSquare から情報収集タスクを自動化するベータツールです。加えて Cree.py は flickr、twitpic.com、yfrog.com、img.ly、plixi.com、twitrpix.com、foleext.com、shozu.com、pickhur.com、moby.to、twitsnaps.com、twitgoo.com などからジオロケーションデータを収集できます。Cree.py はオープンソースのインテリジェンス収集アプリケーションです。Cree.py をインストールするには /etc/apt/sources.list にリポジトリを追加する必要があります。

~~~~
echo "deb http://people.dsv.su.se/~kakavas/creepy/ binary/" >> /etc/apt/sources.list
~~~~

パッケージリストを更新：

~~~~
apt-get update
~~~~

Cree.py をインストール：

~~~~
apt-get install creepy
~~~~

#### Cree.py インターフェース
Cree.py は主にソーシャルネットワーキングプラットフォームや画像ホスティングサービスから**ジオロケーション関連情報**を抽出することを目的としています。収集された情報はアプリケーション内の地図上に表示され、取得したデータとそれに紐づく関連情報（例：その位置から投稿された内容）を示して文脈を提供します。

---

## 2.3 インターネット・フットプリント（Internet Footprint）

インターネット・フットプリントは、後続フェーズで活用可能なターゲットインフラに関する**外部から入手可能な情報**を収集する領域です。

### 2.3.1 メールアドレス（Email addresses）
メールアドレスの収集は一見無意味に思えるかもしれませんが、ターゲット環境に関する貴重な情報を提供します。命名規則の手掛かりや、後の段階でのターゲット候補を示すことがあります。メールアドレス収集には多くのツールがあり、たとえば Maltego が挙げられます。

#### Maltego
Paterva の Maltego は情報収集タスクを自動化するためのツールです。Maltego はオープンソースインテリジェンスおよびフォレンジクスのアプリケーションで、本質的にはデータマイニングと情報収集ツールであり、収集した情報を理解し操作しやすい形式でマップ化します。メールハーベスティングやサブドメインのマッピングなどのタスクを自動化して時間を節約します。Maltego のドキュメントは比較的乏しいため、必要なデータを取得する手順をここに含めます。

Maltego を起動すると、メインインターフェースが表示されます。インターフェースの主要な領域はツールバー、パレット、グラフ（表示）領域、オーバービュー領域、詳細領域、プロパティ領域の六つです。

（スクリーンショット参照）

開始のワークフロー（例、トレーニングとして考えてください）：
1. Maltego の左上で「new graph」をクリックします。  
2. パレットから "domain" アイテムをグラフにドラッグします。グラフ領域ではトランスフォームを処理でき、マイニングビュー、ダイナミックビュー、エッジ加重ビュー、エンティティリストのいずれかでデータを表示できます。最初に domain アイコンを追加するとデフォルトで "paterva.com" になりますので、そのアイコンをダブルクリックしてターゲットのドメインに変更します（www のようなサブドメインは含めない）。これでマイニングを開始できます。

例の流れ：
1. domain アイコンを右クリック（またはダブルクリック）し、"run transform" から "To Website DNS[using search engine]" を選択します。これによりターゲットのサブドメインが一覧表示されるはずです。  
2. すべてのサブドメインを選択し "To IP Address [DNS] transform" を実行します。これによりサブドメインがそれぞれの IP アドレスに解決されます。  
3. 次の論理的ステップはネットブロックの特定で、"To Netblock [Using natural boundaries]" トランスフォームを実行します。

以降は想像力次第で、電話番号、メールアドレス、ジオロケーション情報などをトランスフォームで収集できます。パレットには利用可能（または有効化されている）すべてのトランスフォームが含まれており、本作成時点で約72個のトランスフォームが存在します。Community Edition の制限としては、任意のトランスフォームが返す結果は最大12件であり、Professional 版にはこの制限はありません。

「全トランスフォームを実行する」誘惑には注意してください。データが過剰になり、エンゲージメントに関連する最も興味深いデータへ深掘りする能力を阻害します。

Maltego は事前調査だけでなく、たとえば airodump の CSV/XLS ダンプをインポートしてネットワークを視覚化することにも利用できます。

#### TheHarvester
TheHarvester は Christian Martorella によって書かれたツールで、検索エンジンや PGP キーサーバなどの公開ソースからメールアカウントやサブドメイン名を収集するために使用できます。非常にシンプルですが有効です。

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

TheHarvester は指定したデータソースを検索して結果を返します。これらは後の OSINT ドキュメントに追加すべきです。

例（実行出力）：

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

#### NetGlub
NetGlub は Maltego に非常によく似たオープンソースツールです。NetGlub は情報マイニング／収集ツールで、収集した情報を理解しやすい形式で提示します。現時点で NetGlub のドキュメントはほとんど存在しないため、必要なデータを取得する手順をここに示します。

NetGlub のインストールは容易ではありませんが、次のような手順で行えます（抜粋）：

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

この時点で GUI 用の QT-SDK をインストールします。インストールパスは `/opt/qtsdk` に変更することを推奨します（異なるパスを使う場合は後述のスクリプト内パスを適宜変更してください）。QT-SDK のインストール時に外部依存パッケージのインストールが促されるので、次を実行しておきます：

~~~~
apt-get install libglib2.0-dev libSM-dev libxrender-dev libfontconfig1-dev libxext-dev
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

MySQL を起動し、NetGlub 用データベースを作成します：

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
~~~~

QT の MySQL ドライバをビルドして配置し、NetGlub をビルド・インストールします（抜粋）：

~~~~
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

インストール後の起動手順（四段階）：
1. MySQL が動作していることを確認： `start mysql`  
2. NetGlub Master を起動： `/pentest/enumeration/netglub/master/master`  
3. NetGlub Slave を起動： `/pentest/enumeration/netglub/slave/slave`  
4. NetGlub GUI を起動： `/pentest/enumeration/netglub/qng/bin/unix-debug/netglub`

GUI が表示されれば、Maltego に慣れている人はすぐに操作感を掴めるはずです。インターフェースの主要領域はツールバー、パレット、グラフ（ビュー）領域、詳細、プロパティエリアの六つです（スクリーンショット参照）。

利用可能（または有効化済み）のトランスフォームは約33個あります。トランスフォームは与えられたサイトに対して実際のアクションを行うスクリプトです。

グラフ領域ではトランスフォームの処理結果をマイニングビュー、ダイナミックビュー、エッジ加重ビュー、エンティティリスト等で閲覧できます。オーバービュー領域は発見されたエンティティのミニマップを提供します。詳細領域ではエンティティの特性を掘り下げられ、関係性や情報がどのように生成されたかの詳細を見ることができます。プロパティ領域は、トランスフォームのプロパティに結果が反映された内容を確認できます。

まずは Domain トランスフォームをグラフ領域へドラッグ＆ドロップしてクライアントのドメイン名を設定します。多くの場合 "Run All Transforms" を実行することで初期に必要なデータの大半を収集できます。収集したエンティティのデータを基に追加情報を取得していきます。必要に応じて新しいトランスフォームをドラッグしてプロパティを編集し実行してください。

NetGlub が正しく動作するように、DNS サーバの指定など一部情報を入力する必要があります。また Alchemy と Open Calais の API キーを入力する必要があります。Alchemy の API キーは http://www.alchemyapi.com/api/register.html で、Open Calais の API キーは http://www.opencalais.com/APIkey でそれぞれ取得します。

### Usernames/Handles（ユーザ名／ハンドル）
特定のメールアドレスに関連するユーザ名やハンドルを特定することは有用で、ユーザ名やパスワードの手掛かりになったり、個人の職務外の興味を示したりします。討論グループ（ニュースグループ、メーリングリスト、フォーラム、チャットルーム等）はこの種の情報を探す良い場です。

### Social Networks（ソーシャルネットワーク）
- Check Usernames — 指定ユーザ名が 160 のソーシャルネットワークに存在するか確認するのに有用。

### Newsgroups（ニュースグループ）
- Google — http://www.google.com  
- Yahoo Groups — http://groups.yahoo.com  
- Delphi Forums — http://www.delphiforums.com  
- Big Boards — http://www.big-boards.com

### Mailing Lists（メーリングリスト）
- TILE.Net — http://tile.net/lists  
- Topica — http://lists.topica.com  
- L-Soft CataList（LISTSERV リストの公式カタログ） — http://www.lsoft.com/lists/listref.html  
- The Mail Archive — http://www.mail-archive.com

### Chat Rooms（チャットルーム）
- SearchIRC — http://searchirc.com  
- Gogloom — http://www.gogloom.com

### Forums Search（フォーラム検索）
- BoardReader — http://boardreader.com  
- Omgili — http://www.omgili.com

### Personal Domain Names（個人ドメイン）
ターゲット従業員が所有する個人ドメインを見つけることで、潜在的なユーザ名やパスワードの手掛かり、あるいは個人の興味が判明することがあります。

### Personal Activities（個人の活動）
個人が音声ファイルや動画を作成・公開していることは珍しくありません。些細に見えるこれらも、個人の職務外の興味や手掛かりを与えることがあります。

#### Audio（音声）
- iTunes — http://www.apple.com/itunes  
- Podcast.com — http://podcast.com  
- Podcast Directory — http://www.podcastdirectory.com  
- Yahoo! Audio Search — http://audio.search.yahoo.com

#### Video（動画）
- YouTube — http://youtube.com  
- Yahoo Video — http://video.search.yahoo.com  
- Google Video — http://video.google.com  
- Bing Video — http://www.bing.com/videos

### Archived Information（アーカイブ情報）
ウェブサイトの情報が元のソースで既に入手できない場合があります。アーカイブされたコピーにアクセスできれば過去の情報にアクセス可能です。一般的な手段としては Google のキャッシュ（例: `cache:<site.com>`）や Wayback Machine（http://www.archive.org）を利用します。

（スクリーンショット参照）

### 2.3.7 電子データ（Electronic Data）

諜報・偵察に直接応答して収集される電子データの収集は、ターゲット企業または個人に焦点を合わせるべきです。

#### Document leakage（文書の流出）
公開されている文書は、日付、時刻、場所固有の情報、言語、著者などの重要データを収集するために集めるべきです。収集したデータは現状の環境、運用手順、従業員教育、人事に関する洞察を与える可能性があります。

#### Metadata leakage（メタデータの流出）
メタデータの特定は専門の検索エンジンを使用して行えます。目的はターゲット企業に関連するデータを特定することで、ソーシャルネットワーキング投稿から場所、ハードウェア、ソフトウェアなどを特定できる場合があります。メタデータ検索が可能ないくつかの検索エンジン例：

- ixquick — http://ixquick.com  
- MetaCrawler — http://metacrawler.com  
- Dogpile — http://www.dogpile.com  
- Search.com — http://www.search.com  
- Jeffery's Exif Viewer — http://regex.info/exif.cgi

検索エンジンに加えて、ファイルを収集して各種文書から情報を抽出するツールも存在します。

##### FOCA（Windows）
FOCA は幅広い文書・メディア形式からメタデータを読み取るツールです。FOCA は関連するユーザ名、パス、ソフトウェアバージョン、プリンタ詳細、メールアドレスなどを抽出します。これらはファイルを個別にダウンロードすることなく実行可能です。

##### Foundstone SiteDigger（Windows）
Foundstone の SiteDigger は、Google Hacking Database（GHDB）と Foundstone Database（FSDB）両方の特別なクエリ文字列を使ってドメインを検索するツールです。これにより約 1640 を超えるクエリを利用して追加情報の発見を試みられます。具体的なクエリとその結果は表示され、結果のリンクをダブルクリックするとブラウザで開けます。

##### Metagoofil（Linux/Windows）
Metagoofil はクライアントのウェブサイト上にある公開ドキュメント（.pdf、.doc、.xls、.ppt、.odp、.ods）からメタデータを抽出することを目的とした Linux ベースの情報収集ツールです。Metagoofil は抽出結果を HTML 結果ページとして生成し、ブルートフォース攻撃に有用な潜在的ユーザ名リストも出力します。メタデータからパスや MAC アドレス情報も抽出します。Metagoofil にはいくつかのオプションがあり、主にターゲットや取得結果数に関する指定を行います。

実行例（コマンド）：

~~~~
metagoofil.py -d <client domain> -l 100 -f all -o <client domain>.html -t micro-files
~~~~

##### Exif Reader（Windows）
Exif Reader は Windows 用の画像ファイル解析ソフトウェアです。シャッタースピード、フラッシュの有無、焦点距離など、ほとんどの最新デジタルカメラでサポートされる Exif 形式の情報を解析・表示します。拡張子 JPG の Exif 画像ファイルは通常の JPEG ファイルと同様に扱えます。Exif Reader は JPEG を解析するソフトで、ダウンロード元（原文参照）から入手できます。

##### ExifTool（Windows / OS X）
ExifTool は Windows と OS X 向けのメタ情報読み取りツールです。ExifTool は多数のファイル形式をサポートします。ダウンロード先（原文参照）から入手できます。

##### Image Search（画像検索）
メタデータに直接関係しないものの、Tineye（http://www.tineye.com/）は有用です。もしプロフィールに写真はあるが実名がない場合、Tineye を使って同一画像が使われている他のプロフィールや（出会い系等を含む）詳細情報のあるページを見つけられることがあります。

<<<END>>>

<<<BEGIN>>>
# 2.4 秘密裏の収集（Covert gathering）

## 現地での収集（On-location gathering）
現地訪問（On-Site visits）は評価担当者がターゲットの物理的、環境的、運用上のセキュリティを観察し情報を収集することを可能にします。

### 隣接施設（Adjacent Facilities）
物理的所在地を特定したら、隣接施設（adjacent facilities）を特定することが有用です。隣接施設は文書化し、可能であれば観察された共有施設やサービスも含めてください。

### 物理セキュリティ検査（Physical security inspections）
秘密裏（covert）な物理的セキュリティ検査は、ターゲットのセキュリティ姿勢を把握するために用いられます。これらは隠密に、極秘に、検査されていることをいかなる関係者にも知らせずに実施されます。観察がこの活動の主要要素です。観察すべき物理的セキュリティ対策には、物理的セキュリティ機器、手順、あるいは潜在的な脅威から保護するために使用される装置が含まれます。物理セキュリティ検査は次を含むがこれらに限定されません。

#### 警備員（Security guards）
警備員（またはセキュリティオフィサー）を観察することは、最も目に見える抑止力を評価する第一歩であることが多いです。警備員は制服を着用し、高い可視性を保って不法または不適切な行為を抑止するために活動します。警備員の動きを直接観察することで、使用されている手順を判定したり、移動パターンを把握したりできます。警備員が何を保護しているのかを観察する必要があります。安全な距離から動きを観察するために双眼鏡を利用することも可能です。

一部の警備員は自分自身や保護対象の安全のために銃器の携行を許可され、訓練を受けていることがあります。警備員による銃器の使用が観察されても驚くべきことではありません。これはエンゲージメントを開始する前に文書化しておくべきです。銃器が観察された場合、特別な承認や訓練がない限りそれ以上の行動を取らないよう注意してください。

#### バッジの使用（Badge Usage）
バッジの使用とは、識別バッジをアクセス制御の一形態として利用する物理的セキュリティ手法を指します。バッジシステムは物理アクセス制御システムに連動している場合や、視認による検証手段として単に使われている場合があります。個々のバッジ使用を観察し記録することが重要です。観察により、実際に使用されている特定のバッジを複製できる可能性が出てくることがあります。注目すべき点は、物理的アクセスを得るためにバッジを見えるようにしておく必要があるか、あるいは提示して許可を得る必要があるかどうかです。バッジの使用は文書化し、可能であれば観察された検証手順も含めてください。

#### 施錠装置（Locking devices）
施錠装置は、無許可の出入りを防ぐために実装される機械的または電子的な機構です。単純なドアロックやデッドボルトから暗証番号錠（cipher lock）のような複雑なものまであります。ドアに取り付けられた施錠装置の種類や設置場所を観察することで、そのドアが主に出入口（ingress）として使われているか、または退去（egress）用かを判断できます。施錠装置が何を保護しているかを観察する必要があります。すべての観察は文書化し、可能であれば写真を撮影してください。

#### 侵入検知システム（IDS）／アラーム（Alarms）
（この項は警備員の節と重複しますが、侵入検知やアラームの観察も重要です。）侵入検知システムやアラームの観察は、そのシステムが常時稼働するのかイベント時のみ稼働するのか、モーション検出を使用しているか、IP ベースのカメラを導入しているかなどを把握することが含まれます。これらの情報はシステムの能力と限界を理解するのに役立ちます。

#### セキュリティ照明（Security lighting）
セキュリティ照明は物件に対する予防的および是正的措置として用いられます。侵入者の検出を助け、抑止効果を生み、また単に安全感を高めるために使われることもあります。セキュリティ照明は施設の環境設計の重要な要素です。投光器（floodlights）や低圧ナトリウム灯などが含まれ、夜間常時点灯を意図するものは多くの場合高出力放電ランプ（high-intensity discharge）です。その他、受動赤外線（PIR）センサーなどにより人（または他の哺乳類）が近づいたときのみ点灯するタイプもあります。PIR 活性化ランプは通常瞬時に点灯できる白熱灯が使われることが多く、常時点灯しないため省エネは二次的です。PIR の作動は侵入者に検出されたことを示す抑止効果と、人が突然の明かりの増加に引き寄せられる検出効果とを高めます。いくつかの PIR ユニットはチャイムを鳴らす設定も可能です。ほとんどの現代ユニットは光電セルを備え、暗いときにのみ点灯するようになっています。

施設周囲に適切な照明を配備している場合でも、照明の配置が不適切だと保護対象の視認性を妨げることがある点に注意が必要です。照明は破壊行為（バンダリズム）の対象にされることがあるため、非常に高所に設置するかワイヤーメッシュや堅牢なポリカーボネートで保護すべきです。その他のランプは視界やアクセスから完全に引き込まれ、光をライトパイプを通して導いたり、磨かれたアルミニウムやステンレス鋼の鏡で反射させる方式もあります。同様の理由で高セキュリティ施設は照明に対する予備電源を備えることがあります。使用中の照明の種類、数、位置を観察・文書化してください。

#### 監視／CCTV システム（Surveillance / CCTV systems）
監視／CCTV システムは、施設内外での活動を集中化された場所から観察するために使用されます。監視システムは常時稼働することもあれば、特定のイベントを監視するために必要時のみ作動することもあります。より進んだ監視システムはモーション検出装置で作動することがあり、IP ベースの監視カメラを導入すると分散運用が可能になります。監視カメラは抑止目的で目立つ形のものもあれば、秘匿目的で目立たないものもあります。通常、監視カメラは小型の高解像度カラーカメラで、微細な詳細を解像でき、カメラ制御をコンピュータに接続すると対象物を半自動で追跡することができます。監視/CCTV システムを観察・文書化することは、監視カバー範囲を特定する上で重要です。特定のカメラ型番や正確なカバー範囲を判別できない場合でも、カバー範囲があるかないか、限定的であるかを識別できます。監視システムが物理的に保護されているかを記録してください。保護されていない場合、そのカメラが故意に破壊され得るか、レンズを噴霧や遮蔽でぼかされたり遮られたりする可能性があるか、レーザーで目潰しや破損を受け得るか、ワイヤレスカメラであれば同一周波数での妨害（ジャミング）に対して脆弱であるかなどを文書化する必要があります。

#### アクセス制御装置（Access control devices）
アクセス制御装置は、施設内のエリアや資源へのアクセスを制御するための装置です。アクセス制御は、人（警備員や受付）、機械的手段（鍵と錠）、あるいはアクセス制御システム（例：アクセスコントロール用のヴェスティビュール）のような技術手段によって実現できます。歴史的には鍵と錠で実現されてきましたが、電子アクセス制御が鍵に代わって広く導入されています。

アクセスコントロールリーダーは概ね Basic（基本）、Semi-intelligent（半知能型）、Intelligent（知能型）に分類されます。Basic リーダーはカード番号や PIN を読み取りコントロールパネルに転送するのみです。代表的な Basic リーダーの例として RF Tiny（RFLOGICS）、ProxPoint（HID）、P300（Farpointe Data）などがあります。Semi-intelligent リーダーはドアハードウェア（ロック、ドア接点、退出ボタン）を制御するための入出力を持ちますが、アクセス判断は行いません。一般的な Semi-intelligent リーダーの例は InfoProx Lite IPL200（CEM Systems）や AP-510（Apollo）です。Intelligent リーダーは入力・出力に加え独自にアクセス判断を行うためのメモリと処理能力を持ちます。一般的な Intelligent リーダーの例として InfoProx IPO200（CEM Systems）、AP-500（Apollo）、PowerNet IP Reader（Isonas Security Systems）、ID08（Solus）、Edge ER40（HID Global）、LogLock／UNiLOCK（ASPiSYS Ltd）、BioEntry Plus（Suprema Inc.）などがあります。

一部のリーダーには LCD や機能ボタン（出退勤イベントなどのデータ収集用途）、カメラ／スピーカー／マイクのインターホン機能、スマートカード読み書きサポートなどの追加機能が備わることがあります。使用中のアクセス制御装置の種類、数、設置場所を観察・文書化してください。

### 環境設計（Environmental Design）
環境設計は建物や施設の周辺環境を扱います。物理セキュリティの範囲では、施設の地理、造園、建築、および外観デザインが含まれます。施設や周囲のエリアを観察することで、地形や植栽によって視界が遮られる可能性のある箇所など、懸念となる領域を浮き彫りにできます。建築や外装デザインは警備員が資産を護る能力に影響を与え、視界が低いか存在しない領域を作り出すこともあります。フェンス、保管コンテナ、警備小屋、バリケード、メンテナンス用エリアの配置などは、施設内を秘匿に移動する能力に影響を与える可能性があります。

### 従業員の行動（Employee Behavior）
従業員を観察することはしばしば比較的容易なステップです。従業員の行動は企業の文化や許容される規範について洞察を与えます。観察により使用中の手順や出入りの交通パターン（ingress/egress）を把握できます。安全な距離から双眼鏡で動きを観察することが可能です。

### ダンプスターダイビング（Dumpster diving）
多くのターゲットはゴミ箱やダンプスターに廃棄物を捨てます。これらはリサイクルに基づいて分別されている場合もあります。ダンプスターダイビングとは、商業的または家庭的廃棄物を漁り、所有者が廃棄したが有用な可能性のあるアイテムを探す行為です。これは非常に汚れを伴う行為であることが多いですが、有効な成果をもたらすことがあります。ダンプスターは通常私有地内にあり、評価チームが所有権のない不動産に立ち入ることで不法侵入となる可能性があるため、エンゲージメントの一部としてこれが許可されているかを確認してください。法律の適用は場所によって異なりますが、特に禁止されていない限りダンプスターダイビング自体はしばしば合法です。廃棄物を持ち去る代わりに得られた資料を写真に撮って元のダンプスターに戻す慣行が一般的です。

### RF／ワイヤレス周波数スキャン（RF / Wireless Frequency scanning）
バンドとは無線通信周波数スペクトルの一部で、同様の目的で通常チャネルが使用または割り当てられる範囲のことです。干渉を防ぎ無線スペクトルを効率的に使用するため、類似のサービスは非重複の周波数帯域に配分されます。慣例として、バンドは波長 10^n メートルまたは周波数 3×10^n ヘルツで区切られます。たとえば 30 MHz（10 m）は短波（より低く長い）と VHF（より短く高い）を分ける境界です。これらは周波数帯域の区分であり、周波数割当ではありません。

各バンドには基本的なバンドプランがあり、利用方法や共有方法を定め、干渉を避け、送受信機の互換性のためのプロトコルを設定します。米国ではバンドプランは連邦通信委員会（FCC）によって配分・管理されています。攻撃者が関心を持つであろう二つのバンドは以下のチャートに示されます。

~~~~
バンド名（Band name） | 略称（Abbr） | ITU band | 周波数と波長（空中） | 用途例
---------------------|--------------|----------|-----------------------|------------------------------
Very high frequency  | VHF          | 8        | 30–300 MHz (10 m–1 m) | FM、テレビ放送、地対空／航空間通信、陸上移動体・海上移動体通信、アマチュア無線、気象ラジオ
Ultra high frequency | UHF          | 9        | 300–3000 MHz (1 m–100 mm) | テレビ放送、電子レンジ、携帯電話、無線 LAN、Bluetooth、ZigBee、GPS、二方向無線機（FRS、GMRS）、アマチュア無線
~~~~

RF サイトサーベイ（またはワイヤレスサーベイ）は、特定環境内で使用されている周波数を特定するプロセスです。RF サイトサーベイを行う際には、有効な到達境界を特定することが重要で、これは施設周辺のさまざまなポイントで SNR（信号対雑音比）を測定することを含みます。作業を効率化するため、到着前に使用中の周波数を可能な限り特定しておくべきです。特に警備員が使用する周波数や、ターゲットがライセンスを持つ周波数に注目してください。情報取得に役立つリソースの例：

~~~~
サイト           | URL                                 | 説明
----------------|-------------------------------------|---------------------------------------
Radio Reference | http://www.radioreference.com/apps/db/ | 無料部分で豊富な情報を提供
National Radio Data | http://www.nationalradiodata.com/ | FCC データベース検索 / 年額 $29
Percon Corp     | http://www.perconcorp.com           | FCC データベース検索 / 有料（カスタム料金）
~~~~

最低限、検索エンジン（Google、Bing、Yahoo!）を使って次の検索を行うべきです：
- "Target Company" scanner  
- "Target Company" frequency  
- "Target Company" guard frequency  
- "Target Company" MHz  
- ターゲットに関するラジオ機器・リセラーのプレスリリース  
- 警備アウトソーシング企業の、ターゲット企業との契約に関するプレスリリース

#### 周波数の使用例（Frequency Usage）
周波数カウンタは反復的な電子信号の振動数やパルス数（1秒あたり）を測定する機器です。周波数カウンタやスペクトラムアナライザを使用してターゲット施設周辺で使用されている送信周波数を特定できます。一般的に使われる周波数の例：

~~~~
バンド | 周波数帯域（例）
VHF   | 150–174 MHz
UHF   | 420–425 MHz
UHF   | 450–470 MHz
UHF   | 851–866 MHz
VHF   | 43.7–50 MHz
UHF   | 902–928 MHz
UHF   | 2400–2483.5 MHz
~~~~

スペクトラムアナライザは使用中の周波数を視覚的に示すために使われます。これは通常、周波数カウンタよりも特定レンジに焦点を当てます。たとえばスイープ範囲が 2399–2485 MHz のアナライザ出力は、その範囲で使用中の周波数を明確に示します。施設内外で使用されているすべての周波数帯域を文書化してください。

#### 機器の特定（Equipment Identification）
現地調査の一部として、使用中の無線機やアンテナをすべて特定するべきです。無線機のメーカー・機種、アンテナの長さや種類などを含めます。無線機器の同定に役立つリソースの例：

~~~~
サイト            | URL
-----------------|--------------------------------
HamRadio Outlet  | http://www.hamradio.com
BatLabs          | http://www.batlabs.com
~~~~

802.11 機器の特定は、視覚的にでなくとも RF 放射から判別するのが比較的容易です。視認での特定にはベンダーのウェブサイトで外観や型番を照合するのが有効です。原文に挙げられた主要メーカーのリスト（3com、Apple、Aruba、Atheros、Belkin、Cisco、D-Link、Netgear、Ruckus など）を参照してください。

### 無線 LAN（WLAN）探索（Wireless Local Area Network (WLAN) discovery）
WLAN 探索は、現在展開されている WLAN の種類（未暗号化、WEP、WPA/WPA2、LEAP、802.1x など）を列挙することです。列挙に必要なツールの一例は以下の通りです。

#### Airmon-ng
Airmon-ng はワイヤレスインターフェースをモニタモードにするために使用されます。モニタモードからマネージドモードへ戻すこともできます。USB デバイスが正しく検出されているか確認するために lsusb を使うのが重要です。インストール済みデバイスの検出例では、USB GPS を接続した Prolific PL2303 シリアルポートや Realtek RTL8187 ワイヤレスアダプタが表示されることがあります。次にワイヤレスアダプタが既にモニタモードになっているかを確認します。パラメータ無しで airmon-ng を実行するとインターフェースの状態が表示されます。

モニタモードを開始するには例えば：
~~~~
airmon-ng start wlan0
~~~~

既に mon0 が存在する場合は、前のコマンドを発行する前に以下で破棄します：
~~~~
airmon-ng stop mon0
~~~~

再度 airmon-ng を実行してインターフェース状態を確認します。

#### Airodump-ng
Airodump-ng は Aircrack-ng スイートの一部で、パケットスニッファとして空中のトラフィックを PCAP ファイルや IVS ファイルに格納し、無線ネットワークに関する情報を表示します。生の 802.11 フレームのキャプチャに使われ、特に後で Aircrack-ng で利用するための WEP IV（Initialization Vectors）収集に適しています。GPS 受信機を接続していれば、検出された AP の座標をログできます。Airodump-ng を実行する前に Airmon-ng を使って検出された無線インターフェースを開始してください。

使用例（概要）：
~~~~
airodump-ng <options> <interface> [, <interface>...]
~~~~

主なオプション例（抜粋）：
- --ivs : キャプチャした IV のみ保存  
- --gpsd : GPSd を使用  
- --write <prefix> または -w : ダンプファイルのプレフィックス  
- --beacons : ダンプファイルに全ビーコンを記録  
- --update <secs> : 画面更新間隔（秒）  
- -r <file> : そのファイルからパケットを読む  
- --output-format <formats> : 出力形式（pcap, ivs, csv, gps, kismet, netxml など）

Airodump-ng は検出された AP の一覧と接続クライアント（"stations"）の一覧を表示します。表示の先頭行には現在のチャネル、経過時間、現在日時、オプションで WPA/WPA2 ハンドシェイク検出の有無が示されます。

#### Kismet-Newcore
Kismet-newcore は 802.11 無線 LAN 向けのネットワーク検出器、パケットスニッファ、侵入検知システムです。モニタモードをサポートする任意の無線カードで動作し、802.11a/b/g/n のトラフィックをスニッフィングできます。Kismet はパッシブにパケットを収集して標準的に名の付いたネットワークを検出し、時間をかければ隠蔽されたネットワークを露出させ、データトラフィックからビーコンを出さないネットワークの存在を推定します。

Kismet は以下の 3 部分で構成されます：
- Drones: 無線トラフィックをキャプチャしてサーバに報告する（手動で起動）  
- Server: ドローンに接続しクライアント接続を受け付ける中央部分。トラフィックのキャプチャも可能  
- Client: サーバに接続する GUI 部分

Kismet を正しく動作させるには設定が必要です。まず airmon-ng を実行してモニタモードが設定済みか確認します。複数インターフェースを用いる場合は /etc/kismet/kismet.conf を手動で編集し、使用する各アダプタについて source 行を追加する必要があります（airmon-ng は複数インターフェースを kismet 用に設定できないため）。デフォルトでは Kismet は起動したディレクトリにキャプチャファイルを保存します。これらのキャプチャファイルは Aircrack-ng で利用できます。

コンソールで `kismet` と入力して Enter を押すと Kismet が起動します。サーバ／クライアント構成に関する初期画面が表示され、ローカルでサーバを開始するか既存のサーバを利用するかを選びます。ソースを /etc/kismet/kismet.conf に設定していない場合は、キャプチャ元として使用するインターフェース名（例："mon0"）を指定する必要があります。サーバとクライアントが正常に動作すれば無線ネットワークがリストに現れます。WEP 有効ネットワークなどがハイライトされ、多数のソートオプションが利用可能です。ここでは全機能を解説しませんが、インターフェースに慣れるために操作してみることを推奨します。

#### inSSIDer
Netstumbler を使用していた場合、Windows Vista/7（64bit）では正しく動作しないため失望するかもしれません。しかし代替として Windows XP/Vista/7（32/64bit）で動作する inSSIDer というツールがあり、ネイティブの Wi-Fi API を使用しほとんどの GPS デバイス（NMEA v2.3 以上）と互換性があります。inSSIDer は信号強度（dBi）の時系列追跡、アクセスポイントのフィルタ、Wi-Fi と GPS データを KML ファイルにエクスポートして Google Earth で表示する機能などを備え、Windows 環境での利用に適しています。

~~~~
（Screenshot Here 表示箇所は原文通り）
~~~~

<<<END>>>

<<<BEGIN>>>
# 2.5 外部フットプリンティング（External Footprinting）

外部フットプリンティング段階のインテリジェンス収集は、外部の視点からターゲットに直接働きかけて得られる応答結果を収集することを含みます。目標はターゲットに関する情報を可能な限り多く集めることです。

## IP 範囲の特定（Identifying IP Ranges）
外部フットプリンティングでは、まずどの WHOIS サーバが我々の求める情報を持っているかを特定する必要があります。ターゲットドメインの TLD を把握していれば、対象ドメインが登録されているレジストラ（Registrar）を見つければよいのです。WHOIS 情報はツリー階層に基づいています。ICANN（IANA）は全ての TLD に対する権威あるレジストリであり、手動の WHOIS クエリを行う際の出発点として有用です。

### WHOIS 参照例
- ICANN - http://www.icann.org  
- IANA - http://www.iana.com  
- NRO - http://www.nro.net  
- AFRINIC - http://www.afrinic.net  
- APNIC - http://www.apnic.net  
- ARIN - http://ws.arin.net  
- LACNIC - http://www.lacnic.net  
- RIPE - http://www.ripe.net

適切なレジストラを問い合わせると、登録者（Registrant）情報を取得できます。WHOIS 情報を提供するサイトはいくつかありますが、正確な記録のためには該当するレジストラのみを使用するべきです。
- InterNIC - http://www.internic.net/

## BGP のルッキンググラス（BGP looking glasses）
BGP（Border Gateway Protocol）に参加するネットワークの自律システム番号（ASN）を特定することが可能です。BGP のルート経路は世界中で広報されているため、BGP4 や BGP6 のルッキンググラスを使ってこれらを見つけられます。
- BGP4 - http://www.bgp4.as/looking-glasses  
- BGP6 - http://lg.he.net/

## アクティブ偵察（Active Reconnaissance）
- 手動ブラウジング（Manual browsing）  
- Google Hacking - http://www.exploit-db.com/google-dorks

## パッシブ偵察（Passive Reconnaissance）
- Google Hacking - http://www.exploit-db.com/google-dorks

## アクティブフットプリンティング（Active Footprinting）
アクティブフットプリンティング段階は、ターゲットとの直接的なやり取りに基づいて応答結果を収集することを含みます。

### ゾーン転送（Zone Transfers）
DNS ゾーン転送（AXFR としても知られる）は DNS 取引の一種で、DNS データを含むデータベースを複数の DNS サーバ間で複製するための仕組みです。ゾーン転送にはフル（AXFR）と増分（IXFR）の 2 種類があります。ゾーン転送の可否をテストするためのツールは多数あり、一般的に zone transfer に使われるツールは host、dig、nmap などです。

#### host の例
~~~~
host <domain> <DNS server>
~~~~

#### dig の例
~~~~
dig @server domain axfr
~~~~

### 逆引き DNS（Reverse DNS）
逆引き DNS は、組織内で使用されている有効なサーバ名を取得するために使用できます。ただし、指定された IP アドレスから名前を解決するには PTR（逆引き）DNS レコードが存在する必要がある点に注意してください。PTR レコードが存在すれば結果が返されます。通常は様々な IP アドレスを用いてサーバをテストし、結果が返るかを確認します。

### DNS ブルーティング（DNS Bruting）
クライアントのドメインに関連するすべての情報を特定した後、DNS への問い合わせを開始します。DNS は IP アドレスとホスト名を相互にマップするために使用されるので、設定が不十分かどうかを確認したいのです。DNS を用いてクライアントに関する追加情報を明らかにすることを目指します。DNS に関する深刻な設定ミスの一つは、インターネットユーザが利用可能な状態でゾーン転送を許可していることです。ゾーン転送の可否チェックのみならず、一般には知られていない追加のホスト名を発見する可能性がある DNS 列挙ツールがいくつかあります。

#### Fierce2（Linux）
DNS 列挙には複数のツールがあり、まず注目するのは Fierce2（Fierce の改良版）です。Fierce2 は多くのオプションを持ちますが、ここではゾーン転送を試行する機能に注目します。ゾーン転送が不可能な場合は、登録されているホスト名を列挙するために様々なサーバ名を用いた DNS クエリを実行します。

実行コマンドの例：
~~~~
fierce -dns <client domain> -prefix <wordlist>
~~~~

一般的なプレフィックス（common-tla.txt と呼ばれる）ワードリストが DNS エントリ列挙の際に利用されます。原文では以下の URL にあると記載されています：
https://address-unknown/

#### DNSEnum（Linux）
Fierce2 の代替ツールとして DNSEnum があります。DNSEnum はサブドメインのブルートフォース、逆引き、ドメインのネットワークレンジ一覧、whois クエリなどを通じて DNS を列挙する機能を提供します。また Google スクレイピングを行い追加でクエリすべき名前を収集します。

実行コマンドの例：
~~~~
dnsenum -enum -f <wordlist> <client domain>
~~~~

ここでも共通プレフィックスのワードリストが利用され、原文では同 URL が示されています：
https://address-unknown/

#### Dnsdict6（Linux）
Dnsdict6 は THC IPv6 Attack Toolkit の一部で、IPv6 DNS 辞書ブルートフォーサーです。オプションは比較的単純で、ドメインと辞書ファイルを指定します。

~~~~
（Screenshot Here）
~~~~

## ポートスキャン（Port Scanning）

### Nmap（Windows / Linux）
Nmap（"Network Mapper"）はネットワーク監査／スキャンの事実上の標準ツールです。Nmap は Linux と Windows の両方で動作し、コマンドライン版と GUI 版があります。本書ではコマンドラインに限定して説明します。

Nmap 5.51（ http://nmap.org ）
使用法：
~~~~
nmap [Scan Type(s)] [Options] {target specification}
~~~~

#### TARGET SPECIFICATION:
  ホスト名、IP アドレス、ネットワーク等を渡せます。  
  例: scanme.nmap.org, microsoft.com/24, 192.168.0.1; 10.0.0-255.1-254  
  -iL <inputfilename>: ホスト／ネットワークのリストから入力  
  -iR <num hosts>: ランダムターゲットの選択  
  --exclude <host1[,host2][,host3],...>: スキャン対象から除外  
  --excludefile <exclude_file>: ファイルから除外リストを指定

#### HOST DISCOVERY:
  -sL: List Scan - スキャン対象を単に列挙  
  -sn: Ping Scan - ポートスキャンを行わない  
  -Pn: 全ホストをオンラインとみなす（ホスト検出をスキップ）  
  -PS/PA/PU/PY[portlist]: 指定ポートへの TCP SYN/ACK、UDP、または SCTP 発見  
  -PE/PP/PM: ICMP エコー、タイムスタンプ、ネットマスク要求発見プローブ  
  -PO[protocol list]: IP プロトコル Ping  
  -n/-R: DNS 解決を行わない／常に解決する（デフォルト：場合により）  
  --dns-servers <serv1[,serv2],...>: カスタム DNS サーバを指定  
  --system-dns: OS の DNS リゾルバを使用  
  --traceroute: 各ホストへのホップ経路をトレース

#### SCAN TECHNIQUES:
  -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon スキャン  
  -sU: UDP スキャン  
  -sN/sF/sX: TCP Null, FIN, Xmas スキャン  
  --scanflags <flags>: TCP フラグをカスタマイズ  
  -sI <zombie host[:probeport]>: Idle スキャン  
  -sY/sZ: SCTP INIT/COOKIE-ECHO スキャン  
  -sO: IP プロトコルスキャン  
  -b <FTP relay host>: FTP バウンススキャン

#### PORT SPECIFICATION AND SCAN ORDER:
  -p <port ranges>: 指定ポートのみをスキャン  
    例: -p22; -p1-65535; -p U:53,111,137,T:21-25,80,139,8080,S:9  
  -F: Fast モード - デフォルトより少ないポートをスキャン  
  -r: ポートを連続してスキャン（ランダム化しない）  
  --top-ports <number>: 最も一般的なポートを指定数だけスキャン  
  --port-ratio <ratio>: 指定比率より一般的なポートをスキャン

#### SERVICE/VERSION DETECTION:
  -sV: オープンポートのサービス／バージョン情報をプローブ  
  --version-intensity <level>: 0（軽い）〜9（全プローブ）で設定  
  --version-light: 最も可能性の高いプローブに制限（強度 2）  
  --version-all: すべてのプローブを試行（強度 9）  
  --version-trace: バージョンスキャン活動の詳細を表示（デバッグ用）

#### SCRIPT SCAN:
  -sC: --script=default と同等  
  --script=<Lua scripts>: カンマ区切りのスクリプト／ディレクトリ／カテゴリ  
  --script-args=<n1=v1,[n2=v2,...]>: スクリプトへの引数指定  
  --script-trace: 送受信データを全て表示  
  --script-updatedb: スクリプトデータベース更新

#### OS DETECTION:
  -O: OS 検出を有効化  
  --osscan-limit: 有望なターゲットに限定して OS 検出  
  --osscan-guess: より積極的に OS を推測

#### TIMING AND PERFORMANCE:
  時間指定のオプションは秒（'s'）、ミリ秒（'ms'）、分（'m'）、時（'h'）を付けられます。  
  -T<0-5>: タイミングテンプレート（数値が大きいほど速い）  
  --min-hostgroup/max-hostgroup <size>: 並列ホストスキャングループサイズ  
  --min-parallelism/max-parallelism <numprobes>: プローブ並列度  
  --min-rtt-timeout/...: プローブの往復時間タイムアウト等を調整  
  --max-retries <tries>: プローブ再送の上限  
  --host-timeout <time>: 指定時間後にターゲットを諦める  
  --scan-delay/--max-scan-delay <time>: プローブ間の遅延調整  
  --min-rate / --max-rate: 秒あたりのパケット送信レート制限

#### FIREWALL/IDS EVASION AND SPOOFING:
  -f; --mtu <val>: パケット断片化（MTU 指定可）  
  -D <decoy1,decoy2[,ME],...>: デコイでスキャンを覆い隠す  
  -S <IP_Address>: 送信元アドレスを偽装  
  -e <iface>: 指定インターフェースを使用  
  -g/--source-port <portnum>: 送信元ポートを指定  
  --data-length <num>: 送信パケットにランダムデータを付加  
  --ip-options <options>: 指定 IP オプションで送信  
  --ttl <val>: IP TTL フィールドを設定  
  --spoof-mac <mac address/prefix/vendor name>: MAC アドレス偽装  
  --badsum: 不正な TCP/UDP/SCTP チェックサムを送信

#### OUTPUT:
  -oN/-oX/-oS/-oG <file>: 通常／XML／s|<rIpt kIddi3,／Grepable 形式で出力  
  -oA <basename>: 主要 3 形式を同時に出力  
  -v: 冗長性レベルを上げる（-vv など）  
  -d: デバッグレベルを上げる（-dd など）  
  --reason: ポート状態の理由を表示  
  --open: 開いている（または開いている可能性のある）ポートのみ表示  
  --packet-trace: 送受信パケットをすべて表示  
  --iflist: ホストのインターフェースとルートを表示（デバッグ用）  
  --log-errors: 正常フォーマット出力にエラー／警告を記録  
  --append-output: 指定出力ファイルに追記  
  --resume <filename>: 中断したスキャンを再開  
  --stylesheet <path/URL>: XML 出力を HTML に変換する XSL スタイルシート指定  
  --webxml: Nmap.Org のスタイルシート参照でより移植性の高い XML を生成  
  --no-stylesheet: XML にスタイルシートを関連付けない

#### MISC:
  -6: IPv6 スキャンを有効化  
  -A: OS 検出、バージョン検出、スクリプトスキャン、トレースルートを同時に有効化  
  --datadir <dirname>: カスタム Nmap データファイルの位置指定  
  --send-eth/--send-ip: 生のイーサネットフレームまたは IP パケットで送信  
  --privileged / --unprivileged: 権限の有無を想定  
  -V: バージョン表示  
  -h: ヘルプサマリ表示

#### 例:
  nmap -v -A scanme.nmap.org  
  nmap -v -sn 192.168.0.0/16 10.0.0.0/8  
  nmap -v -iR 10000 -Pn -p 80

詳細や追加のオプション・例についてはマニュアル（http://nmap.org/book/man.html）を参照してください。

Nmap は多数のオプションを備えています。本節はポートスキャンに関連するコマンドに焦点を当てます。使用するコマンドは主に利用可能な時間とスキャンするホスト数に依存します。ホスト数が多く時間が限られているほど調査は簡素化されます。

評価対象の IP セットに基づき、TCP と UDP の両方のポートを 1–65535 の範囲でスキャンすることが望ましいです。使用するコマンド例は以下の通りです：
~~~~
nmap -A -PN -sU -sS -T2 -v -p 1-65535 <client ip range>/<CIDR> or <Mask> -oA NMap_FULL_<client ip range>
nmap -A -PN -sU -sS -T2 -v -p 1-65535 client.com -oA NMap_FULL_client
~~~~

（以下は Nmap 実行時の出力例を示す原文の抜粋を翻訳して保持しています）
- Nmap 実行開始のメッセージ、スクリプトの読み込み、DNS 解決の並列実行、SYN ステルススキャンの開始、65535 ポートのスキャン結果などの記録例があります。スキャンにより 80/tcp が open と検出されたことなどが示されています。

大きな IP セット（100 IP アドレスを超える場合）ではポート範囲を指定しないことが推奨されます。その場合の例：
~~~~
nmap -A -O -PN <client ip range>/<CIDR> or <Mask> -oA NMap_<client ip range>
nmap -A -O -PN client.com -oA NMap_client
~~~~

出力例（抜粋）ではホストが up、rDNS レコード、フィルタ状態のポート数、開いているポートとサービス情報（例: 80/tcp open http Apache httpd 2.2.3 ((CentOS))）などが示されます。また OS 推定が信頼性に乏しい場合の注意書きもあります。

#### IPv6 に関する注意
Nmap は IPv6 に対して限定的なオプションしか持たないことに留意してください。利用可能なオプションには TCP connect（-sT）、Ping スキャン（-sn）、List スキャン（-sL）、バージョン検出などが含まれます。IPv6 の例：
~~~~
nmap -6 -sT -P0 fe80::80a5:26f2:8db7:5d04%12
~~~~

（IPv6 実行例の出力例も原文通りに示され、複数の開いているポートがリストアップされています）

## SNMP スイープ（SNMP Sweeps）
SNMP スイープも実施されます。SNMP はステートレスなデータグラム指向プロトコルで、多くのシステム情報を提供し得ます。ただし不正な community string には応答せず、基盤にある UDP は閉じた UDP ポートを確実に報告しないため、プローブした IP から "no response"（応答無し）が返る場合、以下のいずれかが考えられます：
- マシンに到達できない  
- SNMP サーバが稼働していない  
- 無効な community string  
- 応答データグラムがまだ到着していない

### SNMPEnum（Linux）
SNMPEnum は単一ホストに SNMP リクエストを送り、応答を待ってログする Perl スクリプトです。

~~~~
（Screenshot Here）
~~~~

## SMTP バウンスバック（SMTP Bounce Back）
SMTP バウンスバック（NDR/DSN/NDN、いわゆる bounce）は、配信問題を送信者に通知するためにメールシステムが自動的に返す電子メッセージです。これを利用すると SMTP サーバの指紋特定に役立つ場合があり、バウンスメッセージにサーバのソフトウェア名やバージョン情報が含まれることがあります。

## バナーグラビング（Banner Grabbing）
バナーグラビングはネットワーク上のコンピュータシステムや開放ポート上で動作するサービスについて情報を取得するための列挙技法です。バナーグラビングによりターゲットホストが実行しているアプリケーションや OS のバージョンを特定できます。通常 HTTP、FTP、SMTP（ポート 80、21、25）等で行われ、よく使われるツールは Telnet、nmap、Netcat などです。

#### HTTP の例（原文にある生データの例）
~~~~
JUNK / HTTP/1.0

HEAD / HTTP/9.3

OPTIONS / HTTP/1.0

HEAD / HTTP/1.0
~~~~

（原文の示す他のバナーグラビングの出力例やツールに関する説明は上記に含まれる通りです。）

<<<END>>>

<<<BEGIN>>>
# 2.6 内部フットプリンティング（Internal Footprinting）

内部フットプリンティング段階のインテリジェンス収集は、内部の視点からターゲットと直接やり取りして得られる応答結果を収集することを含みます。目標はターゲットに関する情報を可能な限り多く集めることです。

## 2.6.1 アクティブフットプリンティング（Active Footprinting）
アクティブフットプリンティング段階は、ターゲットとの直接的なやり取りに基づいて応答結果を収集することを含みます。

### 2.6.1.1 Ping スイープ（Ping Sweeps）
アクティブフットプリンティングはライブシステムの特定から始まります。これは通常、どのホストが応答するかを判定するために Ping スイープを行うことで実施されます。

### 2.6.1.2 Nmap（Windows / Linux）
Nmap（"Network Mapper"）はネットワーク監査／スキャンの事実上の標準ツールです。Nmap は Linux と Windows の両方で動作し、コマンドライン版と GUI 版があります。本書ではコマンドラインに限定して説明します。

Nmap 5.51（ http://nmap.org ）

使用法:
~~~~
nmap [Scan Type(s)] [Options] {target specification}
~~~~

#### ターゲット指定（TARGET SPECIFICATION）
ホスト名、IP アドレス、ネットワークなどを渡せます。  
例: scanme.nmap.org, microsoft.com/24, 192.168.0.1; 10.0.0-255.1-254  
- -iL <inputfilename>: ホスト/ネットワークのリストから入力  
- -iR <num hosts>: ランダムにターゲットを選択  
- --exclude <host1[,host2][,host3],...>: ホスト/ネットワークを除外  
- --excludefile <exclude_file>: ファイルから除外リストを指定

#### ホスト検出（HOST DISCOVERY）
- -sL: List Scan — スキャン対象を単に列挙  
- -sn: Ping Scan — ポートスキャンを行わない  
- -Pn: 全ホストをオンラインと見なす（ホスト検出をスキップ）  
- -PS/PA/PU/PY[portlist]: 指定ポートへの TCP SYN/ACK、UDP、または SCTP による検出  
- -PE/PP/PM: ICMP エコー、タイムスタンプ、ネットマスク要求による検出プローブ  
- -PO[protocol list]: IP プロトコル Ping  
- -n/-R: DNS 解決を行わない／常に解決する（デフォルト：場合により）  
- --dns-servers <serv1[,serv2],...>: カスタム DNS サーバを指定  
- --system-dns: OS の DNS リゾルバを使用  
- --traceroute: 各ホストへのホップ経路をトレース

#### スキャン手法（SCAN TECHNIQUES）
- -sS / -sT / -sA / -sW / -sM: TCP SYN / Connect() / ACK / Window / Maimon スキャン  
- -sU: UDP スキャン  
- -sN / -sF / -sX: TCP Null, FIN, Xmas スキャン  
- --scanflags <flags>: TCP フラグをカスタマイズ  
- -sI <zombie host[:probeport]>: Idle スキャン  
- -sY / -sZ: SCTP INIT / COOKIE-ECHO スキャン  
- -sO: IP プロトコルスキャン  
- -b <FTP relay host>: FTP バウンススキャン

#### ポート指定とスキャン順序（PORT SPECIFICATION AND SCAN ORDER）
- -p <port ranges>: 指定ポートのみをスキャン  
  例: -p22; -p1-65535; -p U:53,111,137,T:21-25,80,139,8080,S:9  
- -F: Fast モード — デフォルトより少ないポートをスキャン  
- -r: ポートを連続してスキャン（ランダム化しない）  
- --top-ports <number>: よく使われる上位ポート数をスキャン  
- --port-ratio <ratio>: 指定比率より一般的なポートをスキャン

#### サービス／バージョン検出（SERVICE/VERSION DETECTION）
- -sV: オープンポートのサービス／バージョン情報をプローブ  
- --version-intensity <level>: 0（軽量）〜9（全プローブ）で設定  
- --version-light: 可能性の高いプローブに限定（強度 2）  
- --version-all: すべてのプローブを試行（強度 9）  
- --version-trace: バージョンスキャンの詳細を表示（デバッグ用）

#### スクリプトスキャン（SCRIPT SCAN）
- -sC: --script=default と同等  
- --script=<Lua scripts>: カンマ区切りのスクリプト、スクリプトファイル、またはスクリプトカテゴリ  
- --script-args=<n1=v1,[n2=v2,...]>: スクリプトへの引数  
- --script-trace: 送受信データを全て表示  
- --script-updatedb: スクリプトデータベースを更新

#### OS 検出（OS DETECTION）
- -O: OS 検出を有効にする  
- --osscan-limit: 有望なターゲットに限定して OS 検出  
- --osscan-guess: より積極的に OS を推測

#### タイミングとパフォーマンス（TIMING AND PERFORMANCE）
時間指定のオプションは秒（s）、ミリ秒（ms）、分（m）、時（h）を付けられます。  
- -T<0-5>: タイミングテンプレート（数値が大きいほど速い）  
- --min-hostgroup / --max-hostgroup <size>: 並列ホストスキャンのグループサイズ  
- --min-parallelism / --max-parallelism <numprobes>: プローブ並列度  
- --min-rtt-timeout / --max-rtt-timeout / initial-rtt-timeout <time>: プローブ往復時間のタイムアウト指定  
- --max-retries <tries>: プローブ再送の上限  
- --host-timeout <time>: 指定時間経過後にターゲットを諦める  
- --scan-delay / --max-scan-delay <time>: プローブ間の遅延調整  
- --min-rate / --max-rate: 秒あたりの送信パケット数の最小／最大

#### ファイアウォール／IDS 回避と偽装（FIREWALL/IDS EVASION AND SPOOFING）
- -f; --mtu <val>: パケットを断片化（必要であれば MTU 指定）  
- -D <decoy1,decoy2[,ME],...>: デコイでスキャンを覆い隠す  
- -S <IP_Address>: 送信元アドレスを偽装  
- -e <iface>: 指定インターフェースを使用  
- -g / --source-port <portnum>: 送信元ポートを指定  
- --data-length <num>: 送信パケットにランダムデータを付加  
- --ip-options <options>: 指定 IP オプションで送信  
- --ttl <val>: IP TTL フィールドを設定  
- --spoof-mac <mac address/prefix/vendor name>: MAC アドレスを偽装  
- --badsum: 不正な TCP/UDP/SCTP チェックサムを送信

#### 出力（OUTPUT）
- -oN / -oX / -oS / -oG <file>: 通常、XML、s|<rIpt kIddi3, および Grepable 形式で出力  
- -oA <basename>: 主要 3 形式を同時に出力  
- -v: 冗長度を上げる（-vv などでさらに増加）  
- -d: デバッグ出力を増やす（-dd など）  
- --reason: ポート状態の理由を表示  
- --open: 開いている（または開いている可能性のある）ポートのみ表示  
- --packet-trace: 送受信パケットを全て表示  
- --iflist: ホストのインターフェースとルートを表示（デバッグ用）  
- --log-errors: 正常出力にエラー／警告を記録  
- --append-output: 指定ファイルに追記  
- --resume <filename>: 中断したスキャンを再開  
- --stylesheet <path/URL>: XML を HTML に変換する XSL スタイルシートを指定  
- --webxml: Nmap.Org のスタイルシート参照でより移植性の高い XML を生成  
- --no-stylesheet: XML にスタイルシートを関連付けない

#### 雑多（MISC）
- -6: IPv6 スキャンを有効にする  
- -A: OS 検出、バージョン検出、スクリプトスキャン、トレーサートを同時に有効化  
- --datadir <dirname>: Nmap データファイルのカスタム位置を指定  
- --send-eth / --send-ip: 生のイーサネットフレームまたは IP パケットで送信  
- --privileged / --unprivileged: 権限を想定  
- -V: バージョン表示  
- -h: ヘルプ表示

##### 例:
~~~~
nmap -v -A scanme.nmap.org
nmap -v -sn 192.168.0.0/16 10.0.0.0/8
nmap -v -iR 10000 -Pn -p 80
~~~~

（詳細はマニュアル http://nmap.org/book/man.html を参照）

Nmap は非常に多くのオプションを持ちます。本節ではポートスキャンに必要なコマンドに焦点を当てています。使用するコマンドは主に利用可能な時間とスキャン対象ホスト数に依存します。ホスト数が多く、時間が限られているほど、対象への詳細な問い合わせは減らされます。

Ping スイープを実施するには次のコマンドを利用します:
~~~~
nmap -sn <client ip range>/<CIDR> or <Mask>
~~~~
例:
~~~~
nmap -sn 10.25.0.0/24
~~~~

（原文の Nmap 実行例抜粋）
- Nmap 実行開始、スキャン結果として複数ホストが up と報告され、MAC アドレスやホストの応答時間が示されます。例えば 256 IP アドレス中 4 hosts up といった要約が得られます。

### 2.6.1.3 Alive6（Linux）
Alive6 は THC IPv6 Attack Toolkit の一部であり、IPv6 システムを検出する最も効果的なメカニズムを提供します。オプションは多数ありますが、単純にインターフェースを指定して実行するだけで、ローカルリンク上で稼働中の IPv6 システムを返します。

~~~~
（Screenshot Here）
~~~~

## 2.6.2 ポートスキャン（Port Scanning）
（本節の Nmap に関する説明は上記 2.6.1.2 と同様の内容を含みます。要点は同じく Nmap の使用法、オプション、タイミングとパフォーマンス、回避技術、出力形式、及び実行例に関する説明です。）

基本的な推奨として、評価対象の IP セットに対して TCP と UDP の両方をポート 1–65535 の範囲でスキャンすることが挙げられます。使用例は以下の通りです：
~~~~
nmap -A -PN -sU -sS -T2 -v -p 1-65535 <client ip range>/<CIDR> or <Mask> -oA NMap_FULL_<client ip range>
nmap -A -PN -sU -sS -T2 -v -p 1-65535 client.com -oA NMap_FULL_client
~~~~

（実行ログ例：NSE スクリプトの読み込み、DNS 解決、SYN ステルススキャン、65535 ポートのスキャン実行、80/tcp のオープン検出などの出力が含まれます）

大規模な IP セット（100 アドレス以上）の場合はポート範囲指定を行わないことが推奨されます。例：
~~~~
nmap -A -O -PN <client ip range>/<CIDR> or <Mask> -oA NMap_<client ip range>
nmap -A -O -PN client.com -oA NMap_client
~~~~

IPv6 に関する制限事項や実行例も前述の通りです。

## 2.6.3 SNMP スイープ（SNMP Sweeps）
SNMP スイープは多くの情報を提供するため実施されます。SNMP はステートレスな UDP ベースのプロトコルであり、不正な community string には応答しません。したがって「応答無し」は以下のいずれかを意味する可能性があります：
- マシンが到達不能である  
- SNMP サーバが稼働していない  
- 無効な community string  
- 応答データグラムがまだ到着していない

### SNMPEnum（Linux）
SNMPEnum は単一ホストに対して SNMP リクエストを送り、応答を待ちログする Perl スクリプトです。

~~~~
（Screenshot Here）
~~~~

## 2.6.4 Metasploit
アクティブフットプリンティングは Metasploit によってもある程度実行できます。詳細は Metasploit Unleashed コースなどを参照してください。

## 2.6.5 ゾーン転送（Zone Transfers）
（前述のゾーン転送に関する説明と同様）DNS ゾーン転送（AXFR）は DNS データベースを複製する仕組みで、host、dig、nmap などでテスト可能です。

#### host の例
~~~~
host <domain> <DNS server>
~~~~

#### dig の例
~~~~
dig @server domain axfr
~~~~

## 2.6.6 SMTP バウンスバック（SMTP Bounce Back）
SMTP のバウンスバック（NDR / DSN / NDN、いわゆる bounce）は配信問題を通知する自動メールであり、SMTP サーバの指紋（ソフトウェアやバージョン）を把握する手がかりになることがあります。

## 2.6.7 逆引き DNS（Reverse DNS）
逆引き DNS は組織で使用されるサーバ名を取得するために使えますが、PTR レコードが存在する必要があります。様々な IP を試して結果が返るかを確認します。

## 2.6.8 バナーグラビング（Banner Grabbing）
（前述）バナーグラビングは HTTP、FTP、SMTP（ポート 80、21、25 など）で行われ、ツールとして Telnet、nmap、netcat、netca6（IPv6）などが利用されます。

#### HTTP 生データ例（原文抜粋）
~~~~
JUNK / HTTP/1.0

HEAD / HTTP/9.3

OPTIONS / HTTP/1.0

HEAD / HTTP/1.0
~~~~

### httprint
httprint はウェブサーバ指紋ツールで、サーババナーが偽装されている場合でも特性からサーバを正確に特定することを目標とします。無バナーデバイス（ワイヤレスアクセスポイント、ルータ、スイッチ等）も検出可能です。テキスト署名を用い、署名データベースに署名を追加するのも容易です。

~~~~
（Screenshot Here）
~~~~

## 2.6.9 VoIP マッピング（VoIP mapping）
VoIP マッピングはトポロジ、サーバ、クライアントに関する情報を収集する作業です。目的はライブホスト、PBX の種類とバージョン、VoIP サーバ／ゲートウェイ、クライアント（ハード／ソフト）の種類とバージョンを特定することです。多くの手法は SIP（Session Initiation Protocol）の理解を前提とします。

- SMAP: SIP 対応デバイスをスキャンするためのツール（SIP リクエストを生成して応答を待つ）  
- SIPScan: 単一ホストまたはサブネット全体をスキャンできる SIP スキャナ

~~~~
（Screenshot Here）
~~~~

### エクステンション（Extensions）
エクステンションは IP 電話、PC ソフトフォン、インスタントメッセージクライアント、モバイルデバイスなど、SIP 接続を開始するクライアントアプリケーションやデバイスを指します。目的は有効なユーザ名や SIP デバイスのエクステンションを特定することです。REGISTER、OPTIONS、INVITE といった SIP メソッドのエラーメッセージからエクステンション列挙が可能です。

- Svwar（SIPVicious スイートの一部）: エクステンション列挙に使用可能。範囲または辞書ファイルを使用して列挙を行い、デフォルトは REGISTER メソッドでの列挙です。

~~~~
（Screenshot Here）
~~~~

### enumIAX
Asterisk サーバが使用されている場合、enumIAX のようなユーザ名推測ツールを利用して IAX2（Inter Asterisk Exchange version 2）プロトコルのユーザ名列挙を行う必要があります。enumIAX は逐次推測モードまたは辞書攻撃モードの 2 モードで動作します。

~~~~
（Screenshot Here）
~~~~

## 2.6.10 パッシブ偵察（Passive Reconnaissance）

### 2.6.10.1 パケットスニッフィング（Packet Sniffing）
パケットスニッフィングを行うことで、解析中のストリームに含まれるシステムの IP アドレスや MAC アドレスを収集できます。大部分においてパケットスニッフィングは検出が困難であり、この形態の偵察は本質的にパッシブで極めてステルスです。大量のパケットを収集・分析することで、対象デバイスの OS と稼働サービスのフィンガープリンティングが可能になります。さらに、ログイン情報、パスワードハッシュ、その他の認証情報をパケットストリームから取得できる場合があります。古い Telnet や古い SNMP バージョンは資格情報を平文で送るため、スニッフィングで容易に傍受されます。パケットスニッフィングはどのサーバが重要インフラとして機能しているかを判断するのにも有用であり、攻撃者にとって関心のあるサーバを特定する助けとなります。

~~~~
（ここでパケットスニッフィングに関連するツールや例が挙げられることが想定されます）
~~~~

<<<END>>>

<<<BEGIN>>>
# 3 脆弱性分析（Vulnerability Analysis）

脆弱性分析は、特定された脆弱性がもたらすセキュリティリスクを特定し評価するために用いられます。脆弱性分析作業は大きく二つの領域に分かれます：識別（Identification）と検証（Validation）。識別フェーズの主要要素は脆弱性発見の努力です。検証は、特定された脆弱性の中から実際に有効なものだけに絞り込む作業を指します。

## 3.1 脆弱性テスト（Vulnerability Testing）

脆弱性テストは、アクティブ（Active）とパッシブ（Passive）の両手法を含む形で分類されます。

### アクティブ（Active）

#### 自動化ツール（Automated Tools）
自動化スキャナは、ネットワーク、ホスト、および関連アプリケーションを評価するよう設計されています。現在利用できる自動スキャナには様々なタイプがあり、特定のターゲットやターゲット種別に特化したものもあります。自動化スキャナの核心的目的は、ネットワーク、ホスト、および関連アプリケーション上に存在する脆弱性の列挙です。

##### ネットワーク／一般的な脆弱性スキャナ（Network/General Vulnerability Scanners）

###### Open Vulnerability Assessment System (OpenVAS)（Linux）
Open Vulnerability Assessment System（OpenVAS）は、包括的で強力な脆弱性スキャンおよび脆弱性管理ソリューションを提供する複数のサービスとツールのフレームワークです。OpenVAS は Nessus のフォークであり、非専有ツールの自由な開発を可能にします。  
Nessus の初期バージョン同様、OpenVAS はクライアントとスキャナで構成されます。スキャナを開始するには、コマンドラインから `openvassd` を実行します。

~~~~
（スクリーンショット参照）
~~~~

OpenVAS クライアントは GUI またはコマンドラインのいずれかで実行できます。メニューから OpenVAS Client を選ぶか、コンソールでは "OpenVAS-Client" を実行します。クライアントが起動したらスキャナに接続する必要があります。提供されたユーザ資格情報を入力します。証明書を作成している場合はそれも供給し、表示される証明書を受け入れて続行します。証明書を受け入れると、OpenVAS が初期化され、見つかったプラグイン数と有効化されたプラグイン数を示します。必要なプラグインのダウンロード数に応じて時間がかかることがあります。また、IPv4 と IPv6 の両方のアドレスに対して `/etc/hosts` エントリを適切に追加していることを確認する必要があります。例：

~~~~
127.0.0.1       localhost

127.0.0.1       pentest

# IPv6 対応ホストに望ましい行
::1     ip6-localhost ip6-loopback pentest localhost
~~~~

スキャンを開始する前に OpenVAS のスキャンオプションを設定する必要があります。General セクションは一般的なスキャンオプションを網羅します。新しいスキャンを開始するには Scan Assistant を使用します。Scan Assistant を起動したら、タスクを作成するための情報（タスク名、範囲、ターゲット情報：ホスト名、FQDN、IP アドレス、ネットワークレンジ、CIDR など）を入力し、最後に Execute をクリックしてスキャンを開始します。

（複数のスクリーンショット参照）

------

###### Nessus（Windows/Linux）
Nessus は商用の自動スキャンプログラムで、評価対象のネットワーク、ホスト、および関連アプリケーション上の潜在的脆弱性を検出するよう設計されています。Nessus は特定の評価用にカスタムポリシーを利用できます。非 Web アプリケーションには "Only Safe Checks" ポリシー（付録 A 参照）を、Web アプリケーションには "Only Safe Checks (Web)" ポリシー（付録 B 参照）を使用するべきです。  
Nessus にアクセスするには、正しい URL を Web ブラウザに入力します。Pentest ラボからアクセスする場合の例: `https://<IP ADDRESS>:8834`。

~~~~
（スクリーンショット参照）
~~~~

アクセス用の資格情報は事前に確立しておく必要があります。ログインすると Reports インターフェースが表示されます。Nessus スキャンを実行する前に、最新のシグネチャで正しく更新されているかを検証する必要があります。これは通常スケジュールタスクで行われますが、「About」をクリックしてインストール情報を確認できます。Client Build ID は YYYYMMDD 形式で更新日を示します。例えば 20110223 は 2011 年 2 月 23 日に最後に更新されたことを意味します。スキャナが先週以内に更新されていればスキャンを安全に実施できます。1 週間以上更新されていない場合は直ちに報告し、Nessus が更新されるまで使用を避けるべきです。

Nessus には主に Reports、Scans、Policies、Users の四つのタブがあります。スキャンを開始するには Scans タブを使用し、Add、Edit、Browse、Launch、Pause、Stop、Delete などのオプションが利用できます。新しいスキャンを作成するには Scans → + Add をクリックし、スキャン名、タイプ（Run Now または Template）、ポリシー、ターゲット（ホストを一行ずつ入力するか、ターゲットが記載されたテキストファイルを参照）などのフィールドを入力します。すべて入力したら Launch Scan をクリックしてスキャンを開始します。

注意：自動化ツールはデフォルトで攻撃的すぎることがあり、顧客の業務に影響が出る場合は強度を下げる必要があります。

検証スキャンは毎週 `<IP ADDRESS>` に対して "Validation Scan" ポリシー（付録 C）を用いて実施し、Nessus が正しくスキャンを行っていることを確認するべきです。Validation Scan を実施して同等の結果が得られない場合は直ちに報告し、そのスキャナ利用を停止してください。

スキャン完了後は Reports タブに結果が表示され、該当する完了ファイルをダブルクリックするとスキャン情報と結果が見られます。結果は解析のため保存する必要があります。Download Report をクリックしてフォーマットを指定して保存します。デフォルトは `.nessus` ですが、HTML 形式で保存すると脆弱性のレビューが容易です。

------

###### NeXpose
NeXpose（現 Rapid7 製品）は脆弱性管理、ポリシーコンプライアンス、修復管理を提供する商用スキャナです。アクセス URL の例: `https://<IP ADDRESS>:3780/login.html`。ログイン後はダッシュボードが表示されます。NeXpose を実行する前に、スキャナが最新のシグネチャで更新されているかを検証する必要があります。通常スケジュールで更新されますが、'News' セクションでスキャンエンジンの更新ログやチェックの更新を確認できます。スキャナが先週以内に更新されていればスキャンを安全に実行できます。そうでなければ直ちに報告し、更新されるまで使用を控えるべきです。

NeXpose には Home, Assets, Tickets, Reports, Vulnerabilities, Administration の 6 つの主要タブがあります。スキャンを開始するには 'New Site' を設定します。Site Configuration - General には Site name、Site importance、Site Description などを入力します。Site importance（重要度）はリスク指数計算に影響し、Very Low はリスク指数を 1/3、Low は 2/3、High は 2x、Very High は 3x に変更します。Normal は変更を行いません。

Devices ページで新しいサイトのアセットを一覧化します。IP アドレスやホスト名を手動で入力するか、カンマ区切りのファイルをインポートできます。各行に 1 つのアドレス／ホスト名が必要です。除外ターゲットがある場合は 'Devices to Exclude' エリアで設定します。ターゲットを追加したら 'Scan Setup' でスキャンテンプレートを選択します。スキャンエンジンのドロップダウンでローカルまたは Rapid7 ホストエンジンを選べます。テンプレートを変更すると、そのテンプレートを使っているすべてのサイトに変更が反映されるため注意が必要です。多くのテンプレート（Denial of Service、Discovery scan、Exhaustive、Full audit、PCI 監査 など）が利用可能です（詳細は付録 D）。

スキャンの自動実行をスケジュールすることも可能で、開始日時、最大スキャン時間、繰り返し頻度などを指定できます。スキャンの開始、停止、失敗などを通知するアラートを設定でき、通知方法は SMTP、SNMP、Syslog などが選べます。

資格情報を設定することで、より深いチェック（ポリシー違反、アドウェアやスパイウェア等）を行え、認証済みスキャンはより正確な結果をもたらします。'Credentials' ページで 'New Login' を作成し、ログインタイプを選んでユーザ名／パスワードを入力し、必要ならば Device や Port の制限を設定して Test login を実行します。スキャン完了後は複数のビュー（site、groups、OS、services、software、all assets）で結果を確認でき、'Create Site Report' からレポートのテンプレート選択、配布オプション設定などが可能です。自動レポートの生成や手動実行のいずれでも利用できます。

------

###### eEye Retina
eEye Retina Vulnerability Assessment Scanner は eEye Digital Security による脆弱性スキャナで、Nmap や Nessus の所見を相関・検証するために使われます。初見ではインターフェースが複雑に見えますが、慣れれば扱いやすいです。初期画面は Discovery Tasks ページで、Discovery スキャンにより稼働ホストを判定します。ターゲット指定は単一 IP／ホスト名、IP レンジ、CIDR、Named Host、Address Groups などが指定可能です。Discovery オプションには ICMP Discovery、TCP Discovery（ポートリスト指定）、UDP Discovery、OS 検出、逆引き DNS、NetBIOS 名、MAC アドレス取得などがあります。

Discovery スキャンは即時実行（Discover）またはスケジュール実行（Schedule）できます。Discovery 結果は Results テーブルに表示され、XML 形式でエクスポート（Generate）できます。

主な作業は Audit Interface（監査インターフェース）で行われ、Targets、Ports（All Ports を選択）、Audits（All Audits を選択）、Options（各種列挙や検出オプション）等を確認します。Options には OS 検出、逆引き DNS、NetBIOS 名、MAC 取得、Traceroute、Connect Scan 有効化、Force Scan 有効化、ターゲットリストのランダム化、NetBIOS 経由のレジストリ／ユーザ／共有／ファイル／Hotfix／Named Pipes／Machine Info／Audit Policy／Per-User レジストリ設定／グループ／プロセス列挙、最大 100 ユーザ列挙など多数のオプションがあります。

監査スキャンは即時実行またはスケジュール可能です。自動化ツールは攻撃的すぎる場合があるため、顧客への影響があるときは強度を下げる必要があります。スキャン結果は `.rtd` 形式で保存され、Results テーブルで確認できます。

（複数のスクリーンショット参照）

------

###### Qualys
<寄稿が必要です（<Contribution Needed>）>

###### Core IMPACT
Core IMPACT は情報セキュリティ対策の有効性をテストするためのペネトレーションテストおよびエクスプロイトツールセットです。Core IMPACT はいくつかの困難なエクスプロイトを自動化し、多数のエクスプロイトおよびポストエクスプロイト機能を備えています。

###### Core IMPACT Web
Core は脆弱な Web アプリケーションの SQL インジェクション、Remote File Inclusion、Reflected XSS の脆弱性を悪用できます。以下が一般的なワークフローです：

1) 情報収集：Core は Web 攻撃をシナリオに整理します。複数のシナリオを作成して同一アプリを異なる設定でテストしたり、Web アプリをセグメント化したり、複数アプリを分離できます。ターゲットの選択（URL 指定またはネットワークリポートで発見された Web サーバ選択）、サイト探索方法の選択（自動 or インタラクティブ）などを設定します。自動クロールではブラウザエージェント、最大ページ数と深度、外部ドメインへのリンクの追従有無、サーバ／アプリケーションフレームワーク判定、JavaScript 評価、robots.txt のチェック、フォームの扱いなどを選べます。インタラクティブモードでは Core をプロキシとしてブラウザを設定し、手動でアプリを操作して発見を行います。バックアップや隠しページのチェック等の追加モジュールも利用可能です。

2) Web 攻撃と侵入：攻撃はシナリオまたは個別ページへ実行できます。各エクスプロイトは専用の設定ウィザードを持ちます。SQL インジェクションはリクエストパラメータやクッキーでテストできます。注入攻撃には FAST（一般的なテストのみ）、NORMAL（FAST に追加テスト）、FULL（すべてのテスト）の 3 レベルがあります。XSS テストではブラウザの設定、POST パラメータの評価有無、永続的 XSS の検出有無を設定します。PHP RFI（Remote File Inclusion）では実際に試みるか否かを設定します。実行中は Executed Modules ペインで進行を監視します。成功すれば Core Agents が Entity View の脆弱ページ下に表示されます。

3) Web Apps Browser 攻撃：XSS を利用したソーシャルエンジニアリングを補助できます。ウィザードに従って XSS を悪用し、クライアント情報収集フェーズで得た受信者リストに対して攻撃を仕掛けます。

4) Web App ローカル情報収集：敏感情報の検出、データベースログインやスキーマ取得、コマンド／SQL シェルの可能性確認などを行います。RFI エージェント（PHP）は情報収集、シェルアクセス、またはフル Core Agent のインストールに使用できます。

5) レポート生成：エグゼクティブ、脆弱性、アクティビティなど多様なレポートを生成します。

###### Core Onestep Web RPTs
Core には 2 種類のワンステップ迅速侵入テストがあります：1) WebApps Vulnerability Test — Web アプリを入力すると Core が SQLi、PHP RFI、XSS の脆弱ページを探します（スケジュール可能）。2) WebApps Vulnerability Scanner Validator — IBM AppScan、HP WebInspect、NTOspider のスキャン結果を確認します。

###### Core IMPACT WiFi
Core Impact は 802.11 無線ネットワークとクライアントのペネトレーションテスト用モジュールを含みます。無線モジュールを使うには AirPcap アダプタが必要です。機能にはチャネルスキャン（AP 発見、パケットキャプチャ）、DoS（deauth モジュール）、暗号キークラッキング（WEP/WPA/WPA2 PSK）、Man-in-the-Middle（WiFi クライアントのスニッフィング／改ざん）、偽 AP（Karma Attack）などがあります。レポート機能も備えています。

###### Core IMPACT Client Side
Core Impact はメール、ブラウザ、サードパーティプラグイン等を介したクライアント側の標的型ソーシャルエンジニアリング攻撃を制御・実行できます。メールアドレス収集、PGP、DNS/WHOIS、LinkedIn、サイトのドキュメントメタデータ解析、またはテキストファイルからのインポート等でターゲットリストを生成します。攻撃テンプレートを選び、持続時間やメールテンプレートを設定し（テンプレートは環境に合わせてカスタマイズ推奨）、リンク短縮サービスを利用して誘導し、成功時に収集モジュールを実行するなどの設定が行えます。進行は Executed Modules で監視でき、エージェントが展開されるとネットワークタブに表示されます。クライアント側攻撃完了後に詳細レポートを生成できます。USB トロイ化ドライブの作成で自動的に Core Agent をインストールすることも可能です。

（複数のスクリーンショット参照）

------

###### SAINT
SAINT Professional は SAINTscanner と SAINTexploit を統合した商用スイートで、脆弱性評価と侵入テストのツールキットを提供します。SAINTscanner はネットワークデバイス、OS、アプリケーション内の脆弱性を識別し、コンプライアンス／監査テストにも使えます。SAINTexploit は識別された脆弱性を悪用し、ソーシャルエンジニアリングやフィッシング攻撃のカスタム実行も可能です。ホストが侵害されるとトンネルして他の脆弱ホストへ移動可能です。SAINT はソースからビルドするか、ベンダ提供の仮想マシンを利用して実行できます（推奨）。仮想マシン利用時はアイコンをダブルクリックで起動し、デフォルトパスワードは "SAINT!!!" です。自動更新後にデフォルトブラウザが指定 URL を開きます。

SAINTscanner の使用はログイン後に開始し、スキャンオプション（nmap を使うか内蔵ポートスキャナを使うか、危険なチェックを無効にするか、コンプライアンス項目の有効化等）を設定します。ターゲット制限やスキャンポリシー（プローブオプション、ポリシー構築）を適切に設定したら、IP レンジの挿入、資格情報入力、スキャンポリシー選択、Firewall 設定、Scan Now の順でスキャンを開始します。

SAINTexploit では Discovery、Information Gathering、Single Penetration、Root Penetration、Full Penetration、Web Application といった異なるレベルのペネトレーションテストを選べます。侵害後は Connections タブでセッションに直接操作を行え、コマンドプロンプト、ファイルアップロード／ダウンロードマネージャ、スクリーンショット取得、トンネル等の工具が利用できます。クライアントサイド攻撃用のエクスプロイトも内蔵され、メールテンプレート編集や実行が可能です。

###### SAINTwriter
SAINTwriter はカスタムレポートを生成するコンポーネントで、8 つの事前設定レポート、8 つのレポート形式（HTML、フレームレス HTML、シンプル HTML、PDF、XML、テキスト、タブ区切り、CSV）、および 100 以上のカスタマイズオプションを提供します。

（手順：SAINT GUI → Data → SAINTwriter 等）

------

##### Web アプリケーションスキャナ（Web Application Scanners）

###### 一般的な Web アプリケーションスキャナ（General Web Application Scanners）

###### WebInspect（Windows）
HP の WebInspect は Web アプリケーション層の既知／未知の脆弱性を特定するためのアプリケーションセキュリティ評価ツールです。パラメータインジェクション、クロスサイトスクリプティング、ディレクトリトラバーサルなどの一般的な Web 攻撃を試みます。起動すると Start Page が表示され、Web Site Assessment、Web Service Assessment、Enterprise Assessment、Report 生成、Smart Update の 5 主要機能にアクセスできます。

WebInspect の最初のスキャンは Web Site Assessment で、New Web Site Assessment Wizard を使って設定します。Scan Name を入力し、Assessment Mode（Crawl Only、Crawl and Audit、Audit Only、Manual）を選択します。Crawl Only はサイトの階層構造を完全にマップし、後から Audit を実行できます。Crawl and Audit は発見されたページをその場で監査します。Audit Only はサイトをクロールせず脆弱性検出のみ行います。Manual は手動でサイト内を移動して記録します。認証が必要な場合や、クレデンシャルを埋め込む必要がある場合は Manual を使うことが推奨されます。

通常は最初にサイトをクロールすることが推奨され、フォームのフィルタや無視すべきディレクトリ／ファイル名を特定できます。Assessment Type（Standard、List-Driven、Manual、Workflow-Driven）を選択し、開始 URL を正確に入力します。URL の変種（www あり／なし等）は明確に指定する必要があります。スキャン範囲をフォルダ単位で制限するオプション（Directory only / Directory and subdirectories / Directory and parent directories）もあります。

プロキシ経由でアクセスする必要がある場合は Network Proxy で設定します（Use Internet Explorer / Autodetect / Use PAC File / Use Explicit Proxy Settings / Use Mozilla Firefox など）。ネットワーク認証が必要なら Network Authentication を選択して認証方式と資格情報を入力します。Coverage と Thoroughness オプションは通常変更しませんが、Oracle サイトを最適化する場合は Framework → Optimize scan for でサイトタイプを選びます。

Crawl スライダで Thorough / Default / Normal / Quick のいずれかを選べます。各設定は Redundant Page Detection、Maximum Single URL Hits、Maximum Web Form Submissions、Create Script Event Sessions、Maximum Script Events Per Page、Number of Dynamic Forms Allowed Per Session、Include Parameters In Hit Count といった細かいパラメータを変更します。選択後に Run Profiler Automatically をチェックし、Scan をクリックしてスキャンを開始します。

スキャン中は Navigation ペインにセッションアイコンが表示され、Summary ペインの Vulnerabilities タブや Information タブに可能性のある脆弱性が報告されます。Summary ペイン内の URL をクリックすると関連セッションが強調表示され、Information pane に関連情報が表示されます。Vulnerabilities タブは監査で発見された脆弱性を列挙し、Information タブは監査またはクロールで発見された情報（脆弱性ではないが注目点）をリストします。Best Practices タブは一般的な Web 開発ベストプラクティスに関する検出事項を示します。Scan Log タブは評価のログ（いつどの監査が実行されたか等）を表示し、Server Information タブはサーバに関する興味深い情報を示します。

最終的に解析結果を XML でエクスポートするには File → Export を選び、Scan または Scan Details を選択します。Export Scan Details では Details に Full を選び、必要に応じて SSN、クレジットカードなどのデータをスクラブするオプションを選べます（IP アドレスのスクラブを選ぶとエクスポートデータが解析に使えなくなる点に注意）。Export を実行して保存先を指定します。

###### Web Service Assessment Scan
Web Service Assessment も同様に New Web Service Scan Wizard を使用して設定します。Assessment Mode（Crawl Only、Crawl and Audit）や WSDL ファイルの場所を指定してスキャンを実行します。実行後の結果表示、ナビゲーション、サマリ表示、エクスポート方法は Web Site Assessment と同様です。

（複数のスクリーンショット参照）

###### IBM AppScan
IBM Rational AppScan はアプリケーションセキュリティテストを自動化し、脆弱性を特定して修復提言付きのレポートを生成します。本節は AppScan Standard Edition（デスクトップ版）を対象とします。AppScan はアップデートをツールバーの Update で確認し、インターネットアクセスが必要です。スキャン設定は Configuration Wizard（New → Configuration Wizard）で行い、Web Application Scan をデフォルトとします。開始 URL、ケースセンシティブパス、追加サーバやドメイン、プロキシ・プラットフォーム認証などの設定を行います。認証が必要な場合は Recorded（ログイン手順記録）、Prompt（スキャン中にプロンプト）、Automatic（ユーザ名／パスワードのみのアプリ向け）等のオプションがあります。In-Session 検出オプションを適切に設定しないとスキャン結果は信頼できなくなります。

テストポリシーを選択し、ログイン／ログアウトページでテストを送信するか、セッショントークンのクリアなどのオプションを確認します。アプリ全体のカバレッジを確保するには Manual Explore（手動探索）でアプリをブラウズしてページを発見してからフルスキャンを実行するのが望ましいです。

（複数のスクリーンショット参照）

###### Web Directory Listing / Bruteforcing
DirBuster はディレクトリ／ファイル名をブルートフォースする Java アプリで、隠しディレクトリや難読化されたファイルを発見するために用いられます。効果は使用するワードリストの品質に依存し、DirBuster は 9 種類のリストを持ちます。

###### Webserver Version / Vulnerability Identification
Web サーバのバージョン特定は特定インストール固有の脆弱性を特定する上で重要です。これらの情報は前段階で収集済みであるべきです。

###### NetSparker（Windows）
NetSparker は Windows ベースの Web アプリケーションスキャナで、一般的な Web アプリケーション脆弱性をすべてテストします。NTLM、Forms ベース、証明書ベースの認証を入力でき、報告された所見の確認機能（自動検証）を売りにしています。スキャンは Start Scan、Crawl and Wait、Manual Crawl（Proxy Mode）、Scan Imported Links Only、Schedule Scan などで開始できます。スキャンはクロール → 攻撃の順で進み、発見した脆弱性はブラウザビューや HTTP リクエスト／レスポンス表示で詳述できます。ダッシュボードや脆弱性チャート、PDF/HTML/CSV/XML の報告形式が利用可能です。

（複数のスクリーンショット参照）

###### 専門的な脆弱性スキャナ（Specialized Vulnerability Scanners）
以下は特定領域に特化したスキャナの例です。

- VPN（Virtual Private Networking）検出：VPN はトンネリングによりインターネット上でデータを運ぶため、通常の TCP ポートスキャンでは検出されにくい。`ike-scan` のようなツールが IPsec VPN サーバの検出、フィンガープリンティング、テストに用いられます。
- IPv6：THC-IPV6 Attack Toolkit は IPv6 展開の実装上の脆弱性チェックツール群を提供します（Implementation6、Exploit6 等）。
- War Dialing：モデムで電話番号レンジを自動ダイヤルし、接続可能なモデム／BBS／ファクシミリを探す手法。WarVOX はオーディオを解析することでモデムに限らず多様な電話回線を分類可能にします。
- SIP／VoIP スキャナ：SIPSCAN（REGISTER/OPTIONS/INVITE を用いて拡張子やユーザを列挙）、SIPSAK（OPTION リクエストのみでテスト）、SVMAP（SIPVicious の一部で OPTIONS/INVITE/REGISTER を用いた識別とフィンガープリント）など。

###### WarVOX, iWar, PAW/PAWS（ワーディアル関連）
WarVOX、iWar、PAW／PAWS などのツールは電話網の監査やワードイアルを簡便に行うためのツール群です。VoIP 環境ではネットワーク機器全般（ルータ、VPN ゲートウェイ、TFTP、DNS、DHCP、RADIUS、ファイアウォールなど）をターゲットに含めるべきです。

##### パッシブテスト（Passive Testing）
パッシブテストはその名の通り、受動的に脆弱性を検出する手法です。自動化ツールで行うのが一般的ですが手動でも可能です。

###### 自動化ツール（Automated Tools）
- トラフィックモニタリング（Traffic Monitoring）は、ターゲットのさらなる情報を受動的に収集する仕組みで、OS やネットワークデバイスの特定に役立ちます。アクティブなフィンガープリンティングで古い OS と判定された場合でも、パッシブフィンガープリンティングでそれを確認できます。

###### P0f
P0f は強力なパッシブフィンガープリンティングツールで、接続先・接続元・観測可能な通信に基づき OS を識別できます。

###### Wireshark
Wireshark はオープンソースのパケットアナライザで、ネットワークのトラブルシューティング、分析、プロトコル開発、学習に広く使われます。GTK+ を用いた UI と pcap を利用したパケットキャプチャで、多くの Unix 系 OS と Windows で動作します。

###### Tcpdump
Tcpdump はコマンドラインで動作する一般的なパケットアナライザで、libpcap を使用してネットワーク上の TCP/IP パケットなどをキャプチャ／表示します。Windows 版として WinDump（WinPcap ベース）も存在します。

###### Metasploit スキャナ
Metasploit Framework を活用した脆弱性スキャンについては Metasploit Unleashed コース等で多くのチュートリアルが提供されています。

（この節では各ツールの使用方法、注意点、出力の保存と検証の重要性、及び自動化ツールの攻撃的挙動による副作用の回避について述べました。）
<<<END>>>

<<<BEGIN>>>
# 3.2 脆弱性の妥当性確認（Vulnerability Validation）

## 3.2.1 公開調査（Public Research）

膨大な量のセキュリティ研究の成果として、脆弱性およびそれに関連する概念実証（PoC）やエクスプロイトコードが発見されています。  
脆弱性識別フェーズで得られた結果は、個別に検証される必要があります。エクスプロイトが入手可能な場合、それらを用いて妥当性を確認する必要があります。  
唯一の例外は、サービス拒否（DoS）を引き起こすエクスプロイトであり、これを検証に含めるにはスコープに明記されている必要があります。  

ダウンロード可能なエクスプロイトコードを提供するサイトは多数あり、脆弱性分析フェーズの一部として利用すべきです。例として以下が挙げられます：  

- Exploit-db — http://www.exploit-db.com  
- Security Focus — http://www.securityfocus.com  
- Packetstorm — http://www.packetstorm.com  
- Security Reason — http://www.securityreason.com  
- Black Asylum — http://www.blackasylum.com/?p=160  

---

### 3.2.1.1 一般的／デフォルトパスワード（Common/default passwords）

デバイス、アプリケーション、またはオペレーティングシステムがデフォルト認証情報攻撃に脆弱かどうかを確認するには、既知のデフォルトパスワードを試すだけで簡単に実施できます。  

既知のデフォルトパスワードは、以下のサイトなどから入手可能です：  

- http://www.phenoelit-us.org/dpl/dpl.html  
- http://cirt.net/passwords  
- http://www.defaultpassword.com  
- http://www.passwordsdatabase.com  
- http://www.isdpodcast.com/resources/62k-common-passwords/  

---

## 3.2.2 対象リストの作成（Establish target list）

すべての潜在的ターゲットを特定することは、ペネトレーションテストの成功において極めて重要です。  
適切に作成されたターゲットリストは、攻撃が正確に焦点を定められていることを保証します。  

環境内で稼働しているソフトウェアの特定バージョンが分かれば、テスターは既知の条件に基づいて行動でき、環境を再現することさえ可能です。  

適切に定義されたターゲットリストには、OS バージョン、パッチレベル情報を含めるべきです。  
また、既知であれば Web アプリケーションの脆弱点、アカウントロックアウト閾値、攻撃に適した脆弱ポートなども記載します。  

---

### 3.2.2.1 バージョン対応付け（Mapping Versions）

バージョンチェックは、アプリケーションに関する情報を迅速に識別する方法です。  
ある程度、サービスのバージョンは `nmap` を使用してフィンガープリント可能であり、Web アプリケーションのバージョンは任意のページのソースを確認することで取得できる場合があります。  

---

### 3.2.2.2 パッチレベルの特定（Identifying Patch Levels）

内部ネットワーク内のサービスのパッチレベルを特定するには、システムに問い合わせてバージョン差異を識別できるソフトウェアの使用を検討してください。  

このフェーズでは、クライアントの同意を得た上で認証情報を使用することができます。  
脆弱性スキャナは、認証情報を使用せずにリモートでパッチレベルを特定する際にも非常に有効です。  

---

### 3.2.2.3 脆弱な Web アプリの探索（Looking for Weak Web Applications）

脆弱な Web アプリケーションを特定することは、ペネトレーションテストで特に成果が得やすい活動の一つです。  
注目すべき対象には以下が含まれます：  

- **誤設定された市販（OTS）アプリケーション**  
- **プラグイン機能を持つ市販アプリケーション**（プラグインはしばしばベースアプリより脆弱）  
- **カスタム開発アプリケーション**  

これらを識別するには、`WAFP`（Web Application Fingerprinter）などのツールが非常に効果的です。  

---

### 3.2.2.4 脆弱なポートとサービスの特定（Identify Weak Ports and Services）

脆弱なポートを特定するには、バナーグラビング、`nmap`、および常識的な判断を組み合わせて使用します。  
多くのポートやサービスは、バージョン情報を偽装したり誤解を招く情報を返したりすることがあるため、結果の検証が重要です。  

---

### 3.2.2.5 アカウントロックアウト閾値の特定（Identify Lockout threshold）

認証サービスのロックアウト閾値を特定することで、ブルートフォース攻撃がテスト中に正当なユーザを誤ってロックアウトするのを防止できます。  

環境内のすべての認証サービスを特定し、単一の無害なアカウントを用いてロックアウトが発生するか確認します。  
多くの場合、有効なアカウントに対して **5〜10 回程度の試行**を行えば、ロックアウトの有無を判定できます。  

<<<END>>>