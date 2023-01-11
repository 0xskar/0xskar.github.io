---
title: Walkthrough - Crack the Hash - hash/hashcat/johntheripper/cracking
published: true
---

Hash Cracking Challenges

![0xskar](/assets/crackthehash01.jpg)

* * *

## Level 1

##   48bb6e862e54f2a795ffc4e541caed4d

1. ``hash-identifier`` MD5
2. ``hashcat -m 0 hash1.md5 /usr/share/seclists/Passwords/rockyou.txt`` or ``john hash1.md5 --format=Raw-MD5 --wordlist=/usr/share/seclists/Passwords/rockyou.txt``

##   CBFDAC6008F9CAB4083784CBD1874F76618D2A97 

1. ``hash-identifier`` SHA-1
2. ``hashcat -m 100 hash2.SHA-1 /usr/share/seclists/Passwords/rockyou.txt`` or ``john hash2.SHA-1 --format=Raw-SHA1 --wordlist=/usr/share/seclists/Passwords/rockyou.txt``

##   1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032

1. SHA-256 - can identify hash types with ``hashcat {hash}`` as well as ``hash-identifier``
2. hashcat -m 1400 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032 /usr/share/seclists/Passwords/rockyou.txt

##   $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom

1. Our hint says we need to use lowercase or 4 char passwords because bcrypt takes so long to crack [https://hashcat.net/wiki/doku.php?id=rule_based_attack](https://hashcat.net/wiki/doku.php?id=rule_based_attack)
2. ``hashcat -m 3200 hash4.bcrypt /usr/share/seclists/Passwords/rockyou.txt -r lower4.rule -j '<4'``

##   279412f945939ba78ce0758d3fd83daa

- This wasn't working for me on hashcat or john but googling "MD4 hash cracker online" helped.
- [https://md5decrypt.net/en/Md4/#answer](https://md5decrypt.net/en/Md4/#answer)

* * * 

## Level 2

##   Hash: F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85

1. hash-identifier - SHA-256
2. ``hashcat -m 1400 F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85 /usr/share/seclists/Passwords/rockyou.txt``

##   Hash: 1DFECA0C002AE40B8619ECF94819CC1B

1. hashcat -m 1000 1DFECA0C002AE40B8619ECF94819CC1B /usr/share/seclists/Passwords/rockyou.txt 

##   Hash: $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02. Salt: aReallyHardSalt

1. ``hashcat -m 1800 hashlevel2.1.hash /usr/share/seclists/Passwords/rockyou.txt``
2. ``john hashlevel2.1.hash --format=sha512crypt --wordlist=/usr/share/seclists/Passwords/rockyou.txt`` 
3. This takes a bit of time to crack

##   Hash: e5d8870e5bdd26602cab8dbe07a942c8669e56d6 Salt: tryhackme

1. 160   HMAC-SHA1 (key = $salt) 
2. format it so hashcat can comprehend (``e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme``)
3. ``hashcat -m 160 -a 0 hashlevel2.2.hash /usr/share/seclists/Passwords/rockyou.txt``

* * * 

