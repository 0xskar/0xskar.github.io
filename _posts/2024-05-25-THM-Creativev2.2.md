---
title: THM Creative v2.2
date: 2024-05-25 08:01:00 -0800
categories: [Walkthrough, TryHackMe]
tags: [THM, TryHackMe, CTF]
published: true
---

# Creative v2.2

[TryHackMe](https://tryhackme.com/signup?referrer=62836ffc2c1677004856943b) machine. Exploit a vulnerable web application and some misconfigurations to gain root privileges.

## Enumeration

- `sudo nmap -T4 -p- $target_ip -vvv`
- `sudo nmap -sC -sV -O -p$open_ports $target_ip -vvv`

### Port 80 - Web application 

Nothing to see here on the main website, tried looking for directories but no resuls. Fuzzing for subdomains with wfuzz then add to `/etc/hosts`.

- `wfuzz -c -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -u http://creative.thm -H "Host: FUZZ.creative.thm" --hc 404,301`

Adding `beta.creative.thm` to `/etc/hosts`.

#### beta.creative.thm

Intercept the request in burpsuite and send to the repeater.


