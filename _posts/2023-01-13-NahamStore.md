---
title: NahamStore
date: 2023-01-13 18:32:00 -0500
categories: [Walkthrough, Tryhackme, CTF]
tags: [security, web, bug bounty]
published: false
---

Learning the basics of bug bounty and web application hacking.

[NahamSec](https://twitter.com/nahamsec) set this room was setup to test what people learned on this [Udemy Course](http://bugbounty.nahamsec.training/) "Intro to Bug Bounty Hunting and Web Application Hacking". I havent done the course so going to try this for practice.

We need to start by modifying `/etc/hosts` and adding the domain.

```bash 
10.10.149.84    nahamstore.thm
```
{: file="/etc/hosts" }

Seeing we find an open webserver - going to run `feroxbuser` for directories and files and `wfuzz` for subdomains in the background.

```bash
feroxbuster -u http://nahamstore.thm -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -txt,php
```

```bash
wfuzz -Z -c -u http://FUZZ.nahamstore.htm -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt --sc 200,202,204,301,302,307,403
```

