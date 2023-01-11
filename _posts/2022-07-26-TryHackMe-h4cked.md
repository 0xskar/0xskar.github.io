---
title: Walkthrough - h4cked
published: true
---

Find out what happened by analysing a .pcap file and hack your way back into the machine

[https://tryhackme.com/room/h4cked](https://tryhackme.com/room/h4cked)

* * *

## It seems like our machine got hacked by an anonymous threat actor. However, we are lucky to have a .pcap file from the attack. Can you determine what happened? Download the .pcap file and use Wireshark to view it.

- Download pcap file.

* * *

## The attacker is trying to log into a specific service. What service is this?

```shell
220 Hello FTP World!
USER jenny
331 Please specify the password.
PASS 111111
530 Login incorrect.
USER jenny
331 Please specify the password.
PASS password123
230 Login successful.
```

* * *

## There is a very popular tool by Van Hauser which can be used to brute force a series of services. What is the name of this tool?

- hydra

* * *

## The attacker is trying to log on with a specific username. What is the username?

- jenny

* * *

## What is the user's password?

- password123

* * *

## What is the current FTP working directory after the attacker logged in?

- /var/www/html

* * *

## The attacker uploaded a backdoor. What is the backdoor's filename?

```shell
220 Hello FTP World!
USER jenny
331 Please specify the password.
PASS password123
230 Login successful.
SYST
215 UNIX Type: L8
PWD
257 "/var/www/html" is the current directory
PORT 192,168,0,147,225,49
200 PORT command successful. Consider using PASV.
LIST -la
150 Here comes the directory listing.
226 Directory send OK.
TYPE I
200 Switching to Binary mode.
PORT 192,168,0,147,196,163
200 PORT command successful. Consider using PASV.
STOR shell.php
150 Ok to send data.
226 Transfer complete.
SITE CHMOD 777 shell.php
200 SITE CHMOD command ok.
QUIT
221 Goodbye.
```

* * *

## The backdoor can be downloaded from a specific URL, as it is located inside the uploaded file. What is the full URL?

- [http://pentestmonkey.net/tools/php-reverse-shell](http://pentestmonkey.net/tools/php-reverse-shell)

* * *

## Which command did the attacker manually execute after getting a reverse shell? What is the computer's hostname?

- whoami & wir3

* * *

## Which command did the attacker execute to spawn a new TTY shell?

- python3 -c 'import pty; pty.spawn("/bin/bash")'

* * *

## Which command was executed to gain a root shell?

-  sudo su

* * *

## The attacker downloaded something from GitHub. What is the name of the GitHub project?

- reptile

* * *

## The project can be used to install a stealthy backdoor on the system. It can be very hard to detect. What is this type of backdoor called?

- rootkit

* * *

## The attacker has changed the user's password! Can you replicate the attacker's steps and read the flag.txt? The flag is located in the /root/Reptile directory. Remember, you can always look back at the .pcap file if necessary. Good luck!

```shell
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Chill-Hack]
└─$ hydra -l jenny -P /usr/share/seclists/Passwords/rockyou.txt 10.10.63.85 ftp               
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-07-26 13:40:57
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://10.10.63.85:21/
[21][ftp] host: 10.10.63.85   login: jenny   password: 987654321
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-07-26 13:41:22
```

* * *

## Run Hydra (or any similar tool) on the FTP service. The attacker might not have chosen a complex password. You might get lucky if you use a common word list.
Change the necessary values inside the web shell and upload it to the webserver

-

* * *

## Create a listener on the designated port on your attacker machine. Execute the web shell by visiting the .php file on the targeted web server.

-

* * *

## Become root!

- Follow steps as attacker did

* * *

## Read the flag.txt file inside the Reptile directory

- cat flag.txt

* * * 