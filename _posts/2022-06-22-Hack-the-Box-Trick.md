---
title: Box - HackTheBox - Trick
published: true
---

Hack the Box's Trick Room Walkthrough

![](/assets/hydra.jpeg)

* * *

## Initial nmap scans

1. ``sudo nmap -Pn -sS -T4 -p- 10.129.117.147 -oN nmap_initial``
2. ``sudo nmap -sV -sC -p22,25,53,80 10.10.11.166``

```shell
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 61:ff:29:3b:36:bd:9d:ac:fb:de:1f:56:88:4c:ae:2d (RSA)
|   256 9e:cd:f2:40:61:96:ea:21:a6:ce:26:02:af:75:9a:78 (ECDSA)
|_  256 72:93:f9:11:58:de:34:ad:12:b5:4b:4a:73:64:b9:70 (ED25519)
25/tcp open  smtp    Postfix smtpd
|_smtp-commands: debian.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING
53/tcp open  domain  ISC BIND 9.11.5-P4-5.1+deb10u7 (Debian Linux)
| dns-nsid: 
|_  bind.version: 9.11.5-P4-5.1+deb10u7-Debian
80/tcp open  http    nginx 1.14.2
|_http-title: Coming Soon - Start Bootstrap Theme
|_http-server-header: nginx/1.14.2
Service Info: Host:  debian.localdomain; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

- I think this has soemthing to do with DNS and/or SMTP. debian.localdomain

## Pentesting SMTP 

- smtp-user-enum -M VRFY -U /usr/share/seclists/Usernames/cirt-default-usernames.txt -t 10.10.11.166 

```shell
VRFY root
252 2.0.0 root
```



## Pentesting DNS

- ``dig version.bind CHAOS TXT @10.10.11.166``

```shell 
; <<>> DiG 9.18.1-1-Debian <<>> version.bind CHAOS TXT @10.10.11.166
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 5370
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: e9f0425a1b35ceb3151ab96562bc30023707c0844c6856a2 (good)
;; QUESTION SECTION:
;version.bind.                  CH      TXT

;; ANSWER SECTION:
version.bind.           0       CH      TXT     "9.11.5-P4-5.1+deb10u7-Debian"

;; AUTHORITY SECTION:
version.bind.           0       CH      NS      version.bind.

;; Query time: 88 msec
;; SERVER: 10.10.11.166#53(10.10.11.166) (UDP)
;; WHEN: Wed Jun 29 03:57:06 PDT 2022
;; MSG SIZE  rcvd: 124

```




