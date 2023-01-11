---
title: Walkthrough - GLITCH
published: true
---

Web, Node, RCE, Firefox. Challenge showcasing a web app and simple privilege escalation. Can you find the glitch?

[https://tryhackme.com/room/glitch](https://tryhackme.com/room/glitch)

* * *

## Notes

> This is a simple challenge in which you need to exploit a vulnerable web application and root the machine. It is beginner oriented, some basic JavaScript knowledge would be helpful, but not mandatory. appreciated.

Upon accessing glitch.thm just sent to a page nothing really useful here. Run gobuster.

- `gobuster dir -u http://glitch.thm/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x txt --no-error -t 100`

We find a dir `/secret` that contains a javascript that leads us to another page where we get a token in base64: `dGhpc19pc19ub3RfcmVhbA==` : `this_is_not_real`.

Open up the developer console in firefox; storage > cookies > change to the decoded base64, refresh the page. Here we see that we pull this page from the API. Check the debgger tab in the developer console and look at script.js.

- `curl -X POST http://glitch.thm/api/items?cmd=process.version`

- [NodeJS simple RCE Exploit](https://blog.appsecco.com/nodejs-and-a-simple-rce-exploit-d79001837cc6?gi=ed67b44aebac)

Setup burpsuite to send a shell and setup listener to recieve:

![0xskar](/assets/glitch02.png)

* * * 

## What is your access token?

![0xskar](/assets/glitch01.png)

* * * 

## What is the content of user.txt?

![0xskar](/assets/glitch03.png)

* * * 

## What is the content of root.txt?

- `find / -perm -u=s -type f 2>/dev/null` we find a SUID set @ `/usr/local/bin/doas`

- `tar -cvf firefox.tgz .firefox`
- download and extract and run firefox using the users profile `firefox --profile .firefox/b5w4643p.default-release --allow-downgrade`

![0xskar](/assets/glitch04.png)

- we can use these credentials to login to v0id. Then we can use `doas` which lets us run commands as another user "do-as". Since it has the suid bit set that means we can run it as root and spawn a shell without the root password.

![0xskar](/assets/glitch05.png)

* * * 

