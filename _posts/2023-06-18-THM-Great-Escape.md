---
title: THM Empline
date: 2023-06-15 15:01:00 -0800
categories: [Walkthrough, TryHackMe]
tags: [THM, TryHackMe, CTF]
published: true
---

## The Great Escape

This is my writeup for the Tryhackme box that can be found [here](https://tryhackme.com/room/thegreatescape). There are three different flags for us to find, and a few hints that we will be attacking a docker container, so we will have to escape that and also get onto the machine by penetrating the webapp.

Considering this is a medium level CTF I'll assume that the reader has general knowlege of Kali Linux and its suite of tools.

## A Simple Webapp

Starting off with nmap port and service scans we find a couple open ports, 22 ssh and 80 http.

```
| http-robots.txt: 3 disallowed entries 
|_/api/ /exif-util /*.bak.txt$
```

I am going to add this host to the hostfile for ease of use during the encounter: `escape.thm`.

I am also going to add this to the burp target scope so we can then run feroxbuster through the burp proxy.

### robots.txt

Checking `/robots.txt` we find a few things to check out.

```
User-agent: *
Allow: /
Disallow: /api/
# Disallow: /exif-util
Disallow: /*.bak.txt$
```

Goign to fuzz the /exif-util for extensions that end in .bak.txt

`gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 100 -u http://escape.thm/exif-util/ -x bak.txt --exclude-length 3834 -b 503`

Here I am scanning with gobuster inside of `/exif-util` for the extension `.bak.txt` and excluding the noise which was length 3838 and the status codes 503.