---
title: Protocols and Servers
published: true
---

Learn about common protocols such as HTTP, FTP, POP3, SMTP and IMAP, along with related insecurities.

[https://tryhackme.com/room/protocolsandservers](https://tryhackme.com/room/protocolsandservers)

* * *

## [](#header-2)Task 1 - Introduction

Introduces the user to a few protocols commonly used, such as:

   - HTTP
   - FTP
   - POP3
   - SMTP
   - IMAP

* * * 

## [](#header-2)Task 2 - Telnet 

Telnet is no longer considered a secure option, especially that anyone capturing your network traffic will be able to discover your usernames and passwords, which would grant them access to the remote system. The secure alternative is SSH, which we present in the next room.

##### [](#header-5)Answer the questions below

**To which port will the telnet command with the default parameters try to connect?**

Port 23

* * * 

## [](#header-2)Task 3 - Hypertext Transfer Protocol (HTTP) 

##### [](#header-5)Answer the questions below

**Launch the attached VM. From the AttackBox terminal, connect using Telnet to MACHINE_IP 80 and retrieve the file flag.thm. What does it contain?**

1. ``telnet 10.10.12.91 80``
2. ``GET /flag.thm``
3. THM{e3eb0a1df437f3f97a64aca5952c8ea0}

* * * 

## [](#header-2)Task 4 - File Transfer Protocol (FTP) 

##### [](#header-5)Answer the questions below

**Using an FTP client, connect to the VM and try to recover the flag file. What is the flag? Username: frank Password: D2xc9CgD**

1. ``ftp frank@10.10.12.91``
2. ls 
3. get ftp_flag.thm
4. exit
5. cat ftp_flag.thm
6. THM{364db6ad0e3ddfe7bf0b1870fb06fbdf}

* * * 

## [](#header-2)Task 5 - Simple Mail Transfer Protocol (SMTP) 

##### [](#header-5)Answer the questions below

**Using the AttackBox terminal, connect to the SMTP port of the target VM. What is the flag that you can get?** 

``telnet 10.10.12.91 25`` THM{5b31ddfc0c11d81eba776e983c35e9b5}

* * * 

## [](#header-2)Task 6 - Post Office Protocol 3 (POP3) 

##### [](#header-5)Answer the questions below

**Connect to the VM (10.10.12.91) at the POP3 port. Authenticate using the username frank and password D2xc9CgD. What is the response you get to STAT?** +OK 0 0

**How many email messages are available to download via IMAP on 10.10.12.91?** 0

* * * 

## [](#header-2)Task 7 Internet Message Access Protocol (IMAP) 

##### [](#header-5)Answer the questions below

***What is the default port used by IMAP?*** 143

* * * 

## [](#header-2)Task 8 - Summary 

It is good to remember the default port number for common protocols. Below is a summary of the protocols we covered, sorted in alphabetical order, along with their default port numbers.

| Protocol | TCP Port | Application(s) | Data Security |
|----------|----------|----------------|---------------|
| FTP | 21 | File Transfer | Cleartext |
| HTTP | 80 | Worldwide Web | Cleartext |
| IMAP | 143 | Email (MDA) | Cleartext |
| POP3 | 110 | Email (MDA) | Cleartext |
| SMTP | 25 | Email (MTA) | Cleartext |
| Telnet | 23 | Remote Access | Cleartext |

* * * 