---
title: Passive Recon
date: 2022-06-15 18:32:00 -0500
categories: [Lesson, Tutorial]
tags: [Enumeration, TryHackMe, whois, nslookup, dig]
---

Learn about the essential tools for passive reconnaissance, such as whois, nslookup, and dig.

[https://tryhackme.com/room/passiverecon](https://tryhackme.com/room/passiverecon)

* * *

## Task 1 - Introduction

In this room, after we define passive reconnaissance and active reconnaissance, we focus on essential tools related to passive reconnaissance. We will learn three command-line tools:

   - whois to query WHOIS servers
   - nslookup to query DNS servers
   - dig to query DNS servers

We use whois to query WHOIS records, while we use nslookup and dig to query DNS database records. These are all publicly available records and hence do not alert the target.

We will also learn the usage of two online services:

   - [DNSDumpster](https://dnsdumpster.com/)
   - [Shodan.io](https://www.shodan.io/)

These two online services allow us to collect information about our target without directly connecting to it.

* * * 

## Task 2 - Passive Versus Active Recon 

In passive reconnaissance, you rely on publicly available knowledge. It is the knowledge that you can access from publicly available resources without directly engaging with the target.

   - Looking up DNS records of a domain from a public DNS server.
   - Checking job ads related to the target website.
   - Reading news articles about the target company.

Active reconnaissance, on the other hand, cannot be achieved so discreetly. It requires direct engagement with the target. Think of it like you check the locks on the doors and windows, among other potential entry points.

   - Connecting to one of the company servers such as HTTP, FTP, and SMTP.
   - Calling the company in an attempt to get information (social engineering).
   - Entering company premises pretending to be a repairman.

**You visit the Facebook page of the target company, hoping to get some of their employee names. What kind of reconnaissance activity is this? (A for active, P for passive)** Passive

**You ping the IP address of the company webserver to check if ICMP traffic is blocked. What kind of reconnaissance activity is this? (A for active, P for passive)** Active

**You happen to meet the IT administrator of the target company at a party. You try to use social engineering to get more information about their systems and network infrastructure. What kind of reconnaissance activity is this? (A for active, P for passive)** Active

## Task 3 - Whois 

![0xskar](/assets/passiverecon1.png)

**When was TryHackMe.com registered?** 20180705

**What is the registrar of TryHackMe.com?** namecheap.com

**Which company is TryHackMe.com using for name servers?** cloudflare.com

* * * 
 
## Task 4 - nslookup and dig 

Name Server Look Up (``nslookup -type=OPTIONS DOMAIN_NAME``) where OPTIONS is a query from the table below:

| Query type | Result |
|------------|--------|
| A | IPv4 Addresses |
| AAAA | IPv6 Addresses |
| CNAME | Canonical Name |
| MX | Mail Servers |
| SOA | Start of Authority |
| TXT | TXT Records |

Domain Information Groper (``dig DOMAIN_NAME OPIONS``)

##   Answer the questions below

**Check the TXT records of thmlabs.com. What is the flag there?** ``dig thmlabs.com TXT`` or ``nslookup -type=TXT thmlabs.com``

* * * 

## Task 5 - DNSDumpster 

Pretty useful took for helping map out sub domain structures and other DNS queries.

**Lookup tryhackme.com on DNSDumpster. What is one interesting subdomain that you would discover in addition to www and blog?** remote.

* * * 

## Task 6 - Shodan.io 

Shodan gathers information about all devices directly connected to the Internet. If a device is directly hooked up to the Internet then Shodan queries it for various publicly-available information. The types of devices that are indexed can vary tremendously: ranging from small desktops up to nuclear power plants and everything in between.

**According to Shodan.io, what is the 2nd country in the world in terms of the number of publicly accessible Apache servers?** Germany

**Based on Shodan.io, what is the 3rd most common port used for Apache?** 8080

**Based on Shodan.io, what is the 3rd most common port used for nginx?** 8888

* * * 

## Task 7 - Summary 

We covered command-line tools, whois, nslookup, and dig. We also discussed two publicly available services DNSDumpster and Shodan.io. 

| Purpose | Commandline Example |
|---------|---------------------|
| Lookup WHOIS record | whois tryhackme.com |
| Lookup DNS A records | nslookup -type=A tryhackme.com |
| Lookup DNS MX records at DNS server | nslookup -type=MX tryhackme.com 1.1.1.1 |
| Lookup DNS TXT records | nslookup -type=TXT tryhackme.com |
| Lookup DNS A records | dig tryhackme.com A |
| Lookup DNS MX records at DNS server | dig @1.1.1.1 tryhackme.com MX |
| Lookup DNS TXT records | dig tryhackme.com TXT |

* * * 
