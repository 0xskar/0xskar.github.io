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



**What is Jonah's cracked password?**



**What is the user flag?**



**What is the root flag?**




* * * 
