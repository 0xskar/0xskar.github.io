---
title: Walkthrough - Agent T
published: true
---

Security, PHP, Backdoor. Something seems a little off with the server.

[https://tryhackme.com/room/agentt](https://tryhackme.com/room/agentt)

* * *

## Notes

Simple nmap scan shows us one port 80 webserver that contains a simple admin dashboard that doesnt seem to go anywhere. After fumbling around for a bit capturing the site in burpsuite, find that the site is powered by php/8.1.0-dev. 

![0xskar](/assets/agentt01.png)

Easy enough to find [an exploit on exploitDB](https://www.exploit-db.com/exploits/49933). 

> An early release of PHP, the PHP 8.1.0-dev version was released with a backdoor on March 28th 2021, but the backdoor was quickly discovered and removed. If this version of PHP runs on a server, an attacker can execute arbitrary code by sending the User-Agentt header. The following exploit uses the backdoor to provide a pseudo shell ont the host.

The exploit on exploitdb isn't very good. It wont let us travel through directories. [flast101 wrote a better one here](https://github.com/flast101/php-8.1.0-dev-backdoor-rce/blob/main/revshell_php_8.1.0-dev.py) that will connect and send us back a reverse shell.

## What is the flag?

![0xskar](/assets/agentt02.png)

* * * 

