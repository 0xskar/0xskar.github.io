---
title: Walkthrough - KoTH Food CTF
published: true
---

Tags: KoTH, Linux, Sudo, Web.
Description: Practice Food KoTH alone, to get familiar with KoTH!
Difficulty: Easy
URL: [https://tryhackme.com/room/kothfoodctf](https://tryhackme.com/room/kothfoodctf)

* * *

## Notes

> This is room for one of the King of the Hill machines, FoodCTF. Capture the food and all the flags, while you're at it.

> Find all 8 flags

```
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 28:0c:0c:d9:5a:7d:be:e6:f4:3c:ed:10:51:49:4d:19 (RSA)
|   256 17:ce:03:3b:bb:20:78:09:ab:76:c0:6d:8d:c4:df:51 (ECDSA)
|_  256 07:8a:50:b5:5b:4a:a7:6c:c8:b3:a1:ca:77:b9:0d:07 (ED25519)
3306/tcp  open  mysql   MySQL 5.7.29-0ubuntu0.18.04.1
| mysql-info: 
|   Protocol: 10
|   Version: 5.7.29-0ubuntu0.18.04.1
|   Thread ID: 5
|   Capabilities flags: 65535
|   Some Capabilities: FoundRows, IgnoreSpaceBeforeParenthesis, SwitchToSSLAfterHandshake, ConnectWithDatabase, InteractiveClient, IgnoreSigpipes, SupportsCompression, Speaks41ProtocolOld, LongColumnFlag, SupportsTransactions, ODBCClient, LongPassword, Support41Auth, SupportsLoadDataLocal, Speaks41ProtocolNew, DontAllowDatabaseTableColumn, SupportsAuthPlugins, SupportsMultipleResults, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: &l0\x0B\x08kH\x02_\x02\x1D\x18^2\x07G\x18g\x1E\x14
|_  Auth Plugin Name: mysql_native_password
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=MySQL_Server_5.7.29_Auto_Generated_Server_Certificate
| Not valid before: 2020-03-19T17:21:30
|_Not valid after:  2030-03-17T17:21:30
9999/tcp  open  abyss?
| fingerprint-strings: 
|   FourOhFourRequest, GetRequest, HTTPOptions: 
|     HTTP/1.0 200 OK
|     Date: Mon, 29 Aug 2022 17:09:39 GMT
|     Content-Length: 4
|     Content-Type: text/plain; charset=utf-8
|     king
|   GenericLines, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|_    Request
15065/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Host monitoring
16109/tcp open  unknown
| fingerprint-strings: 
|   GenericLines: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Date: Mon, 29 Aug 2022 17:09:39 GMT
|     Content-Type: image/jpeg
|     JFIF
|     #*%%*525EE\xff
|     #*%%*525EE\xff
|     $3br
|     %&'()*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
|     &'()*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
|     Y$?_
|     qR]$Oyk
|_    |$o.
46969/tcp open  telnet  Linux telnetd
```

- `find / -type f -name *flag* 2>/dev/null`

/home/tryhackme/flag7
/home/bread/flag - got
/home/food/.flag - got
/var/flag.txt - got


```
7.7M -rwxrwxr-x 1 bread     bread     7.7M Apr  6  2020 /home/bread/main
7.1M -rwxrwxr-x 1 tryhackme tryhackme 7.1M Mar 20  2020 /home/tryhackme/img
```
/etc/alternatives/my.cnf
/home/pasta/.gnupg/pubring.kbx                                                                                                             
/home/pasta/.gnupg/trustdb.gpg
/home/pasta/.config/lxc/config.yml
/var/log/journal/c214c9d4231b4554bf4c0d97704f5dcf/system.journal
/var/log/journal/c214c9d4231b4554bf4c0d97704f5dcf/user-1002.journal
/var/log/kern.log
/var/log/syslog
-rwxr-xr-x 1 root root 465928 Jul 20  2018 /usr/bin/screen.old 
/usr/share/dns/root.key



* * * 

## Flag 1

Connecting to telnet `telnet 10.10.139.19 46969` gives us a possible entryway with a ROT cipher which translates to `food:givemecookies`. Using these creds connects us to a GNU bash that has limited commands. However it seems calling to the full path executes commands. This gets us into the user `food`

- `/bin/cat .flag`

* * * 

## Flag 2

Checking the site on port 16109 there is an index.html which is actually a jpeg. Using `stegseek -sf` on that we get some creds `pasta:pastaisdynamic` we can ssh into the server with these creds.

```
pasta@foodctf:/home/bread$ cat flag
```

* * * 

## Flag 3

- `pasta@foodctf:/home/food$ cat .flag`

* * * 

## Flag 4

```
pasta@foodctf:~$ cat /var/flag.txt 
thm{0c48608136e6f8c86aecdb5d4c3d7ba8}
```

* * * 

## Flag 5



* * * 

## Flag 6



* * * 

## Flag 7



* * * 

## Flag 8



* * * 

