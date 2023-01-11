---
title: Box - RootMe
published: true
---

TryHackMe CTF Box - Can you root me?

[https://tryhackme.com/room/rrootme](https://tryhackme.com/room/rrootme)

![0xskar](/assets/rootme01.jpg)

* * *

## Reconnaissance 

Gather Information

##   Answer the questions below

**Scan the machine, how many ports are open?**

- ``sudo nmap -Pn -sS -T4 10.10.130.16 -vvv -p-``
- ``nmap -sC -sV -O -T4 -vvv -p22,80``

**What version of Apache is running?**

- 80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.29 ((Ubuntu))

**What service is running on port 22?**

- ssh

**Find directories on the web server using the GoBuster tool. What is the hidden directory?**

- ``gobuster dir -u http://10.10.130.16/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x txt,html,php -z -t 100``

```shell
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.130.16/
[+] Method:                  GET
[+] Threads:                 100
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              txt,html,php
[+] Timeout:                 10s
===============================================================
2022/07/05 13:21:06 Starting gobuster in directory enumeration mode
===============================================================
/css                  (Status: 301) [Size: 310] [--> http://10.10.130.16/css/]
/js                   (Status: 301) [Size: 309] [--> http://10.10.130.16/js/] 
/uploads              (Status: 301) [Size: 314] [--> http://10.10.130.16/uploads/]
/index.php            (Status: 200) [Size: 616]                                   
/panel                (Status: 301) [Size: 312] [--> http://10.10.130.16/panel/]  
/server-status        (Status: 403) [Size: 277] 
```

* * * 

## Getting a Shell 

Find a form to upload and get a reverse shell, and find the flag.

- Php is not permitted but phtml is.

##   Answer the questions below

**user.txt**

- upgrade shell ``python -c 'import pty; pty.spawn("/bin/bash")'``

![0xskar](/assets/rootme02.png)

* * * 

## Privilege escalation  

Now that we have a shell, let's escalate our privileges to root.

##   Answer the questions below

**Search for files with SUID permission, which file is weird?**

- ``find / -perm -u=s -type f 2>/dev/null``
- /usr/bin/python

**Find a form to escalate your privileges.**

- [https://gtfobins.github.io/gtfobins/python/#suid](https://gtfobins.github.io/gtfobins/python/#suid)

**root.txt**

![0xskar](/assets/rootme03.png)

* * * 

