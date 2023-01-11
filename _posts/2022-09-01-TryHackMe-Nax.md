---
title: Walkthrough - Nax
published: true
---

Tags: Security.
Description: Identify the critical security flaw in the most powerful and trusted network monitoring software on the market, that allows an user authenticated execute remote code execution.
Difficulty: Easy
URL: [https://tryhackme.com/room/nax](https://tryhackme.com/room/nax)

* * *

## Notes

- `sudo nmap -sC -sV -sT -O 10.10.249.249 -p22,25,80,389,443,5667 -oN nmap_2`

```
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 62:1d:d9:88:01:77:0a:52:bb:59:f9:da:c1:a6:e3:cd (RSA)
|   256 af:67:7d:24:e5:95:f4:44:72:d1:0c:39:8d:cc:21:15 (ECDSA)
|_  256 20:28:15:ef:13:c8:9f:b8:a7:0f:50:e6:2f:3b:1e:57 (ED25519)
25/tcp   open  smtp       Postfix smtpd
|_smtp-commands: ubuntu.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=ubuntu
| Not valid before: 2020-03-23T23:42:04
|_Not valid after:  2030-03-21T23:42:04
80/tcp   open  http       Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
389/tcp  open  ldap       OpenLDAP 2.2.X - 2.3.X
443/tcp  open  ssl/http   Apache httpd 2.4.18 ((Ubuntu))
| tls-alpn: 
|_  http/1.1
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=192.168.85.153/organizationName=Nagios Enterprises/stateOrProvinceName=Minnesota/countryName=US
| Not valid before: 2020-03-24T00:14:58
|_Not valid after:  2030-03-22T00:14:58
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_ssl-date: TLS randomness does not represent time
5667/tcp open  tcpwrapped
```

Upon visiting the site and runnning gobuster scans we find a Nagios XL installation and a homepage filled with elements.

![0xskar](/assets/nax01.png)

* * * 

## What hidden file did you find?

- Converting these elements to their element number and feeding to cyberchef guves us `/PI3T.PNg`. 

* * * 

## Who is the creator of the file?

- `exiftool PI3T.PNg` Piet Mondrian

* * * 

## If you get an error running the tool on your downloaded image about an unknown ppm format -- open it with gimp or another paint program and export to ppm format, and try again!

After looking for hours I found out piet is a picture type programming language and there are commands we can use to get data from piet compiled images.

* * * 

## What is the username you found?

- `npiet -e 220000 PI3T.ppm`
- `nagiosadmin`

* * * 

## What is the password you found?

- `n3p3UQ&9BjLp4$7uhWdY`

* * * 

## What is the CVE number for this vulnerability? This will be in the format: CVE-0000-0000

Searchsploit `Nagios XI ` and we have authentication so find the CVE with `cat 48191.rb | grep CVE`

* * * 

## Now that we've found our vulnerability, let's find our exploit. For this section of the room, we'll use the Metasploit module associated with this exploit. Let's go ahead and start Metasploit using the command `msfconsole`.



* * * 

## After Metasploit has started, let's search for our target exploit using the command 'search applicationame'. What is the full path (starting with exploit) for the exploitation module?

- `exploit/linux/http/nagios_xi_plugins_check_plugin_authenticated_rce`

* * * 

## Compromise the machine and locate user.txt Locate root.txt

![0xskar](/assets/nax02.png)

* * * 

