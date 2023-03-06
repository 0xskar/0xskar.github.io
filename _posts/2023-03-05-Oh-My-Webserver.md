---
title: Oh My Webserver
date: 2023-03-02 02:47:00 -0500
categories: [Walkthrough, Tryhackme, CTF]
tags: [web, apache, CVE-2021-41773]
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

### CVE-2021-41773

