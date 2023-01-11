---
title: Walkthrough - ToolsRus
published: true
---

[https://tryhackme.com/room/toolsrus](https://tryhackme.com/room/toolsrus)

* * *

## Hacking

We get to use the following tools

- Dirbuster
- Hydra
- Nmap
- Nikto
- Metasploit

```shell
22/tcp   open  ssh     syn-ack OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    syn-ack Apache httpd 2.4.18 ((Ubuntu))
8009/tcp open  ajp13   syn-ack Apache Jserv (Protocol v1.3)

22/tcp   open  ssh     syn-ack ttl 61
80/tcp   open  http    syn-ack ttl 61
1234/tcp open  hotline syn-ack ttl 61
8009/tcp open  ajp13   syn-ack ttl 61
```

* * * 

## What directory can you find, that begins with a "g"?

- ``gobuster dir -u http://10.10.192.13/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -t 100 -x txt,html,php --no-error``

* * * 

## Whose name can you find from this directory?

- Visit ``/guidelines/`` - bob

* * * 

## What directory has basic authentication?

- ``/protected/``

* * * 

## What is bob's password to the protected part of the website?

- ``hydra -l bob -P /usr/share/seclists/Passwords/rockyou.txt 10.10.192.13 http-get /protected``
- ``[80][http-get] host: 10.10.192.13   login: bob   password: bubbles``

* * * 

## What other port that serves a webs service is open on the machine?

- ``sudo nmap -Pn -sS -p- -T4 10.10.192.13 -vvv``

* * * 

## Going to the service running on that port, what is the name and version of the software?

Answer format: Full_name_of_service/Version

- Apache Tomcat/7.0.88

* * * 

## Use Nikto with the credentials you have found and scan the /manager/html directory on the port found above.

How many documentation files did Nikto identify?

- ``nikto -h http://10.10.192.13:1234/manager/html -id bob:bubbles -Format xml -o niktoscan``
- scan took a few hours...

* * * 

## What is the server version (run the scan against port 80)?

- ``nmap -sV -sT -p22,80,8009 -T4 10.10.192.13 -vvv``

* * * 

## What version of Apache-Coyote is this service using?

- ``nmap -sV -sT -p22,80,8009 -T4 10.10.192.13 -vvv``

* * * 

## Use Metasploit to exploit the service and get a shell on the system.

- ``multi/http/tomcat_mgr_upload``

**What user did you get a shell as?**

- root

**What text is in the file /root/flag.txt**

- ``cat flag.txt``

* * * 