---
title: Walkthrough - Anonymous
published: true
---

Tags: Security, Linux, Permissions, Medium
Description: Not the hacking group.
Difficulty: Medium
URL: [https://tryhackme.com/room/anonymous](https://tryhackme.com/room/anonymous)

* * *

## Notes

start the machine

we get two flags for the machine, we're also told to get the flags we are required to have a basic understanding of linux, well here we go lmao... 

enumerate the ports, how many are open? 

- `sudo nmap $TARGET_IP -Pn -sS -p- -vvv -oN nmap_1`

```
Discovered open port 139/tcp on 10.10.96.182
Discovered open port 445/tcp on 10.10.96.182
Discovered open port 22/tcp on 10.10.96.182
Discovered open port 21/tcp on 10.10.96.182
```

what service is running on port 21?

```
PORT    STATE SERVICE     REASON  VERSION
21/tcp  open  ftp         syn-ack vsftpd 2.0.8 or later
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.2.127.225
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 111      113          4096 Jun 04  2020 scripts [NSE: writeable]
```

and ports 139 and 445? samba shares

```
139/tcp open  netbios-ssn syn-ack Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn syn-ack Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
```

lets connect to the machine and view the share on the user's computer

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Anonymous]
└─$ smbclient -L $TARGET_IP                                 
Password for [WORKGROUP\0xskar]:

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        pics            Disk      My SMB Share Directory for Pics
        IPC$            IPC       IPC Service (anonymous server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            ANONYMOUS
                                                                                                                                                                                                                  
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Anonymous]
└─$ smbclient \\\\$TARGET_IP\\pics
Password for [WORKGROUP\0xskar]:
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Sun May 17 04:11:34 2020
  ..                                  D        0  Wed May 13 18:59:10 2020
  corgo2.jpg                          N    42663  Mon May 11 17:43:42 2020
  puppos.jpeg                         N   265188  Mon May 11 17:43:42 2020

                20508240 blocks of size 1024. 13306812 blocks available
smb: \> get corgo2.jpg 
getting file \corgo2.jpg of size 42663 as corgo2.jpg (36.0 KiloBytes/sec) (average 36.0 KiloBytes/sec)
smb: \> get puppos.jpeg 
getting file \puppos.jpeg of size 265188 as puppos.jpeg (162.8 KiloBytes/sec) (average 109.4 KiloBytes/sec)
smb: \> exit
```

some corgies long live the queen, oh shit...

hiding anything?










* * * 

