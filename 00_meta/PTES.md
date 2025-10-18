# PTES Technical Guidelines（PTES 技術ガイドライン）

このセクションは、ペネトレーションテストの実施中に従うべき特定の手順を定義するのに役立つ **PTES の技術ガイドライン** として設計されています。注意すべき点として、ここにあるのは業界で用いられてきた **ベースラインの手法** に過ぎないということです。これらは、コミュニティによっても、あなた自身の標準の中でも、継続的に更新・変更されていく必要があります。ガイドラインとはあくまでガイドラインであり、特定の状況で進むべき方向を示し支援するためのものであって、ペネトレーションテストのやり方を包括的に規定する **完全な手順書ではありません**。**発想を柔軟に** 持ちましょう。

> 以下目次だけ表示。実際に確認したい箇所は公式サイトを参照<br>
> http://www.pentest-standard.org/index.php/PTES_Technical_Guidelines

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
