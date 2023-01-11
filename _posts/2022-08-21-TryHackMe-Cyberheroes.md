---
title: Walkthrough - CyberHeroes
published: true
---

Tags: Security, CTF, Credential, Authentication.
Description: Want to be a part of the elite club of CyberHeroes? Prove your merit by finding a way to log in!
Difficulty: Easy
URL: [https://tryhackme.com/room/cyberheroes](https://tryhackme.com/room/cyberheroes)

* * *

## Notes

- `sudo nmap -Pn -sS -T4 -p- 10.10.237.5 -vvv`

```
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 61
80/tcp open  http    syn-ack ttl 60
```

- `sudo nmap -sC -sV -sT -O -p22,80 cyberheroes.thm`

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 cf:80:3f:97:0d:34:f4:cf:03:8f:cd:a7:59:a0:0f:1d (RSA)
|   256 20:f5:aa:46:99:19:04:d1:9c:10:b6:b6:5e:91:92:0b (ECDSA)
|_  256 0b:87:2f:e0:99:7f:0f:7f:88:46:ae:cb:83:13:75:6d (ED25519)
80/tcp open  http    Apache httpd 2.4.48 ((Ubuntu))
|_http-title: CyberHeros : Index
|_http-server-header: Apache/2.4.48 (Ubuntu)
```

- `gobuster dir -u http://cyberheroes.thm -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 100 -x txt --no-error`

- incredibly easy challenge, view source of login.html to get credentials to login for the flag.

* * * 

## What is the content of user.txt?



* * * 

## What is the content of root.txt?



* * * 

