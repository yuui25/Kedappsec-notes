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
| FOCA | http://www.informatica64.com/DownloadFOCA | 公開ドキュメントのメタデータ解析によりサイトの情報を収集するツール。 |  |
| THC IPv6 Attack Toolkit | http://www.thc.org/thc-ipv6 | IPv6 と ICMP6 の脆弱性を突くツール群。 |  |
| THC Hydra | http://thc.org/thc-hydra/ | 高速なログオンブルートフォースツール。多くのサービスに対して並列攻撃が可能。 | * |
| Cain | http://www.oxid.it/cain.html | Windows 向けのパスワードリカバリツール。ネットワークスニッフィング、辞書/ブルートフォース、VoIP録音、ハッシュ解析など多機能。 | * |
| cree.py | http://ilektrojohn.github.com/creepy/ | SNS や画像ホスティングから位置情報を収集し、地図上に表示するツール。 |  |
| inSSIDer | http://www.metageek.net/products/inssider | Windows 用の GUI ベース Wi-Fi 発見／トラブルシュートツール。 | * |
| Kismet Newcore | http://kismetwireless.net | 802.11 レイヤ2 無線検出器・スニッファ・侵入検知システム。モニターモード対応アダプタでパッシブにパケット収集が可能。 |  |
| Rainbow Crack | http://project-rainbowcrack.com | レインボーテーブルを用いたハッシュクラッキングツール。 |  |
| dnsenum | http://code.google.com/p/dnsenum | whois を拡張した DNS 列挙ツール。サブドメイン発見や BIND バージョン調査などに利用。 | * |
| dnsmap | http://code.google.com/p/dnsmap | サブドメイン総当たりによる DNS マッピング用の受動的ツール。 | * |
| dnsrecon | http://www.darkoperator.com/tools-and-scripts/ | Ruby で書かれた DNS 列挙スクリプト。TLD 拡張、SRV 列挙、ゾーン転送、逆引き等を行う。 | * |
| dnstracer | http://www.mavetju.org/unix/dnstracer.php | 指定ドメインの DNS 情報の由来をたどるツール。 | * |
| dnswalk | http://sourceforge.net/projects/dnswalk | DNS データベースの整合性チェックを行うデバッガ。ゾーン転送や一貫性チェックを実施。 | * |
| Fierce | http://ha.ckers.org/fierce | ノンコンティグな IP 範囲を探索するドメインスキャンツール。 | * |
| Fierce2 | http://trac.assembla.com/fierce/ | Fierce の更新版。開発者グループによるメンテナンスあり。 | * |
| FindDomains | http://code.google.com/p/finddomains | マルチスレッドのドメイン発見ツール。大量の IP に対応する仮想ホスト/サブドメイン発見に有用。 | * |
| HostMap | http://hostmap.lonerunners.net | 指定 IP に紐づく全ホスト名・仮想ホストを自動発見するツール。 | * |
| URLcrazy | http://www.morningstarsecurity.com/research/urlcrazy/ | ドメインのタイプミス候補を生成し、スカッティングや類似ドメインを発見する。 | * |
| theHarvester | http://www.edge-security.com/theHarvester.php | 検索エンジンや PGP キーサーバ等からメールアドレス／ユーザ名／ホスト名を収集する情報収集ツール。 | * |
| The Metasploit Framework | http://metasploit.com | リモートエクスプロイトやポストエクスプロイテーションツール群の集積。定期的な更新（svn 等）で新機能・エクスプロイトが追加されるため常時更新が望ましい。 | * |
| The Social-Engineer Toolkit (SET) | http://www.secmaniac.com/download/ | 人間要素に対する高度な攻撃を実行するためのフレームワーク。偽メールや偽サイト生成などソーシャルエンジニアリングに特化。 | * |
| Fast-Track | http://www.secmaniac.com/download/ | 自動化されたペンテストスイート。多くの攻撃はクライアント側のデータサニタイズ不備やパッチ未適用に起因。Metasploit 3 に依存。 | * |

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

| Name | URL | Description/Focus |
|---|---|---|
| Academia.edu | http://www.academia.edu | アカデミック向け |
| Advogato | http://www.advogato.org | OSS 開発者コミュニティ |
| aNobii | http://www.anobii.com/anobii_home | 書籍 |
| aSmallWorld | http://www.asmallworld.net | 欧州ジェットセット向け |
| AsianAvenue | http://www.asianave.com | アジア系米国人向け |
| Athlinks | http://www.athlinks.com | ランニング／スイミング |
| Audimated.com | http://www.audimated.com | インディ音楽 |
| Avatars United | http://www.avatarsunited.com | オンラインゲーム |
| Badoo | http://badoo.com | 出会い系（欧州・中南米で人気） |
| Bebo | http://www.bebo.com | 一般 |
| ... | ... | ... |
| Facebook (IPv4) | http://www.facebook.com | 一般（SNS） |
| Twitter | http://twitter.com | マイクロブログ |
| LinkedIn | http://www.linkedin.com | ビジネス／プロフェッショナル |
| Flickr | http://www.flickr.com | 写真共有 |
| YouTube | http://youtube.com | 動画共有 |
| Myspace | http://www.myspace.com | 一般 |
| Tumblr | http://www.tumblr.com | マイクロブログ |
| Vkontakte | http://vkontakte.ru/ | ロシア語圏向け大手SNS |
| Foursquare | http://foursquare.com | 位置情報ベースのSNS |
| ... | ... | （上記は抜粋。実運用では対象者に合わせて該当サイトを網羅的に調査） |

（上表は原文列挙を抜粋して整理したもので、実際の調査では対象者の国・言語・興味に応じた追加サイトを参照してください。）

### 2.2.3　Cree.py
Cree.py（creepy）は、Twitter や Foursquare 等から **位置情報（geolocation）関連情報を自動収集**するベータツールです。flickr、twitpic、yfrog、plixi、twitrpix、shozu など多くの画像投稿サイトから位置情報を引き出せるため、位置特定に有効です。Cree.py は OSINT アプリケーションで、Debian 系ではリポジトリ追加→apt-get でインストール可能です（以下は要点のみ）。


# 例（Debian 系）
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
./theHarvester.py -d <domain> -b google -l 500
# -d: ドメイン or 会社名
# -b: データソース (google, bing, pgp, linkedin 等)
# -l: 結果数上限
~~~~

出力には発見したアカウント（例：admin@client.com）などが並びます。OSINT ドキュメントに加えて後工程で利用します。

#### 2.3.1.3　NetGlub
NetGlub は Maltego に類似した OSS のデータマイニングツールで、グラフインターフェースを持ち transforms を多数実行できます。導入は依存関係の解決や QT-SDK、Graphviz、MySQL のセットアップ等が必要で手順はやや複雑です。インストール後は Master/Slave/GUI を起動してグラフベースで調査を行います。Alchemy/OpenCalais の API キーを設定して高度な解析を行う仕様などがあります。

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
ドキュメントや画像のメタデータから、ファイル作成者、使用ソフトウェア、ファイルパス、プリンタ情報、MAC アドレス等が判明することがあります。以下は代表的なツール／手法です。

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
画像のトレースや類似画像検索には (TinEye)[http://www.tineye.com/] 等が有用です。匿名プロフィールに掲載された写真の逆検索で、当該人物の別プロファイルや実名を突き止められる場合があります。
