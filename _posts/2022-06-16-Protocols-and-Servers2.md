---
title: Protocols and Servers
date: 2022-06-16 18:32:00 -0500
categories: [Lesson, Tutorial]
tags: [tcpdump, mitm, protocols, OSI]
---

Learn about attacks against passwords and cleartext traffic; explore options for mitigation via SSH and SSL/TLS.

[https://tryhackme.com/room/protocolsandservers2](https://tryhackme.com/room/protocolsandservers2)

* * *

## Task 1 - Introduction

This room will focus on how a protocol can be upgraded or replaced to protect against disclosure and alteration, i.e. protecting the confidentiality and integrity of the transmitted data. We will be recommending other modules that cover additional topics.

And we get to use Hydra to find weak passwords.

* * * 

## Task 2 - Sniffing Attack

There are many programs available to capture network packets. We consider the following:

   1. Tcpdump is a free open source command-line interface (CLI) program that has been ported to work on many operating systems.
   2. Wireshark is a free open source graphical user interface (GUI) program available for several operating systems, including Linux, macOS and MS Windows.
   3. Tshark is a CLI alternative to Wireshark.

- ``sudo tcpdump port 110 -A`` would capture a username and password of a user checking their email messages using POP3. This would also require access to the network traffic via a wiretap or a switch with port mirroring. We need ``sudo`` as capturing packets requires root, POP3 uses ``port 110`` and to display captures packes in ACSII we need the ``-A`` flag. 

This type of attack if only possible on TELNET and nobody uses TELNET anymore but if they do for some reason here we go.

##   Answer the questions below

**What do you need to add to the command ``sudo tcpdump`` to capture only Telnet traffic?** port 23

**What is the simplest display filter you can use with Wireshark to show only IMAP traffic?** imap

* * * 

## Task 3 - Man-in-the-Middle (MITM) Attack 

Any time you browse over HTTP, you are susceptible to a MITM attack, and the scary thing is that you cannot recognize it. Many tools would aid you in carrying out such an attack, such as [Ettercap](https://www.ettercap-project.org/) and [Bettercap](https://www.bettercap.org/).

##   Answer the questions below

**How many different interfaces does Ettercap offer?** Ettercap offers three interfaces, traditional command line, GUI and ncurses

**In how many ways can you invoke Bettercap?** 3

* * * 

## Task 4 - Transport Layer Security (TLS) 

The common protocols we have covered so far send the data in cleartext; this makes it possible for anyone with access to the network to capture, save and analyze the exchanged messages. The image below shows the ISO/OSI network layers. The protocols we have covered so far in this room are on the application layer. Consider the ISO/OSI model; we can add encryption to our protocols via the presentation layer. Consequently, data will be presented in an encrypted format (ciphertext) instead of its original form.

![0xskar](/assets/protocolsandservers01.png)

TLS is more secure than SSL, and it has Boxly replaced SSL. We could have dropped SSL and just written TLS instead of SSL/TLS, but we will continue to mention the two to avoid any ambiguity because the term SSL is still in wide use. However, we can expect all modern servers to be using TLS.

An existing cleartext protocol can be upgraded to use encryption via SSL/TLS. We can use TLS to upgrade HTTP, FTP, SMTP, POP3, and IMAP, to name a few. The following table lists the protocols we have covered and their default ports before and after the encryption upgrade via SSL/TLS. The list is not exhaustive; however, the purpose is to help us better understand the process.

| Protocol | Default Port | Secured Protocol | Default Port with TLS |
|----------|--------------|------------------|-----------------------|
| HTTP | 80 | HTTPS | 443 |
| FTP | 21 | FTPS | 990 | 
| SMTP | 25 | SMTPS | 465 | 
| POP3 | 110 | POP3S | 995 | 
| IMAP | 143 | IMAPS | 993 | 

To establish an SSL/TLS connection, the client needs to perform the proper handshake with the server. Based on RFC 6101, the SSL connection establishment will look like the figure below.

![0xskar](/assets/protocolsandservers02.png)

##   Answer the questions below

**DNS can also be secured using TLS. What is the three-letter acronym of the DNS protocol that uses TLS?**

DoT = DNS over TLS

* * * 

## Task 5 - Secure Shell (SSH) 

- ``scp user@target:/directory/file ~/location`` this will copy a file from the target to our specified location on our machine. 
- ``scp ~/location user@target:/directory/`` this will copy a file from our machine to our target

FTP could be secured using SSL/TLS by using the FTPS protocol which uses port 990. It is worth mentioning that FTP can also be secured using the SSH protocol which is the SFTP protocol. By default this service listens on port 22, just like SSH.

##   Answer the questions below

**Use SSH to connect to 10.10.127.14 as mark with the password XBtc49AB. Using uname -r, find the Kernel release?** 5.4.0-84-generic

**Use SSH to download the file book.txt from the remote system. How many KBs did scp display as download size?** 415KB

* * * 

## Task 6 - Password Attack 

- ``hydra -l username -P wordlist.txt server service``

Where

   - ``-l`` username: ``-l`` should precede the username, i.e. the login name of the target.
   - ``-P wordlist.txt``: ``-P`` precedes the wordlist.txt file, which is a text file containing the list of passwords you want to try with the provided username.
   - ``server`` is the hostname or IP address of the target server.
   - ``service`` indicates the service which you are trying to launch the dictionary attack. which can also be specified with ftp:// or http://

   Extra optional arguments that you can add:

   - ``-s`` PORT to specify a non-default port for the service in question.
   - ``-V`` or ``-vV``, for verbose, makes Hydra show the username and password combinations that are being tried. This verbosity is very convenient to see the progress, especially if you are still not confident of your command-line syntax.
   - ``-t`` n where n is the number of parallel connections to the target. ``-t 16`` will create 16 threads used to connect to the target.
   - ``-d``, for debugging, to get more detailed information about what’s going on. The debugging output can save you much frustration; for instance, if Hydra tries to connect to a closed port and timing out, ``-d`` will reveal this right away.

In summary, attacks against login systems can be carried out efficiently using a tool, such as THC Hydra combined with a suitable word list. Mitigation against such attacks can be sophisticated and depends on the target system. A few of the approaches include:

   - Password Policy: Enforces minimum complexity constraints on the passwords set by the user.
   - Account Lockout: Locks the account after a certain number of failed attempts.
   - Throttling Authentication Attempts: Delays the response to a login attempt. A couple of seconds of delay is tolerable for someone who knows the password, but they can severely hinder automated tools.
   - Using CAPTCHA: Requires solving a question difficult for machines. It works well if the login page is via a graphical user interface (GUI). (Note that CAPTCHA stands for Completely Automated Public Turing test to tell Computers and Humans Apart.)
   - Requiring the use of a public certificate for authentication. This approach works well with SSH, for instance.
   - Two-Factor Authentication: Ask the user to provide a code available via other means, such as email, smartphone app or SMS.
   - There are many other approaches that are more sophisticated or might require some established knowledge about the user, such as IP-based geolocation.

##   Answer the questions below

**We learned that one of the email accounts is lazie. What is the password used to access the IMAP service on 10.10.140.139?**

1. ``nmap -sC -sV 10.10.140.139 -T 4 -F`` IMAP service is on port 143
2. ``hydra -t 16 -l lazie -P /usr/share/wordlists/rockyou.txt imap://10.10.140.139``
3. ``[143][imap] host: 10.10.140.139   login: lazie   password: butterfly``

* * *

## Task 7 - Summary 

It is good to remember the default port number for common protocols.

| Protocol | TCP Port | Application(s) | Data Security |
|----------|----------|----------------|---------------|
| FTP | 21 | File Transfer | Cleartext |
| FTPS | 990 | File Transfer | Encrypted |
| HTTP | 80 | Worldwide Web | Cleartext |
| HTTPS | 443 | Worldwide Web | Encrypted |
| IMAP | 143 | Email (MDA) | Cleartext |
| IMAPS | 993 |  Email (MDA) | Encrypted |
| POP3 | 110 | Email (MDA) | Cleartext |
| POP3S | 995 | Email (MDA) | Encrypted |
| SFTP | 22 | File Transfer | Encrypted |
| SSH | 22 | Remote Access and File Transfer | Encrypted |
| SMTP | 25 | Email (MTA) | Cleartext |
| SMTPS | 465 | Email (MTA) | Encrypted |
| Telnet | 23 | Remote Access | Cleartext |

Hydra remains a very efficient tool that you can launch from the terminal to try the different passwords. We summarize its main options in the following table.

| Option | Explanation |
|--------|-------------|
| ``-l username`` | Provide the login name |
| ``-P WordList.txt`` | Specify the password list to use |
| ``server service`` | Set the server address and service to attack |
| ``-s PORT`` | Use in case of non-default service port number |
| ``-V`` or ``-vV`` | Show the username and password combinations being tried |
| ``-d`` | Display debugging output if the verbose output is not helping |

* * * 



