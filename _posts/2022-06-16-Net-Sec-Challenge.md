---
title: Network Secutiry Challenge
date: 2022-06-16 18:32:00 -0500
categories: [Lesson, Tutorial]
tags: [nmap, telnet, hydra, Enumeration, TryHackMe]
---

Practice the skills you have learned in the Network Security module.

[https://tryhackme.com/room/netsecchallenge](https://tryhackme.com/room/netsecchallenge)

* * *

## Task 1 - Introduction

Use this challenge to test your mastery of the skills you have acquired in the Network Security module. All the questions in this challenge can be solved using only ``nmap``, ``telnet``, and ``hydra``.

* * * 

## Task 2 - Challenge Questions 

##   Answer the questions below

**What is the highest port number being open less than 10,000?**

1. ``nmap -sC -sV -T 4 10.10.82.60 -p- -oN initial`` scan with nmap and save scan to "initial" for refrence.
2. There are 6 open ports but one is hidden, the last question is a clue?
3. Redoing nmap scan but with ``-sN`` and sudo
4. We can see port 8080

**There is an open port outside the common 1000 ports; it is above 10,000. What is it?**

- inital scan shows port 10021

**How many TCP ports are open?**

- We see 5 open TCP ports and 

**What is the flag hidden in the HTTP server header?**

1. ``telnet 10.10.82.60 80``
2. ``GET / HTTP/1.1``
3. ``host: telnet``
4. hit return a few times to get flag: THM{web_server_25352}

**What is the flag hidden in the SSH server header?**

1. We got this flag using our initial Nmap scan: THM{946219583339}

**We have an FTP server listening on a nonstandard port. What is the version of the FTP server?**

- initial scan shows "vsftpd 3.0.3"

**We learned two usernames using social engineering: eddie and quinn. What is the flag hidden in one of these two account files and accessible via FTP?**

1. add users eddit and quin to txt file
2. ``hydra -t 16 -L users.txt -P /usr/share/wordlists/rockyou.txt ftp://10.10.82.60 -s 10021`` specifying port because not common
3. user eddie pass jordan - user quinn pass andrea
4. ``ftp quinn@10.10.82.60 -P 10021``
5. ``get ftp_flag.txt``
6. ``exit``
7. ``cat ftp_flag.txt`` THM{321452667098}

**Browsing to http://MACHINE_IP:8080 displays a small challenge that will give you a flag once you solve it. What is the flag?**

- we get the flag from running ``nmap -sN`` sening null bytes THM{f7443f99} 

* * * 