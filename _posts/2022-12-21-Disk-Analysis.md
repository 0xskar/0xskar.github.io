---
title: Disk Analysis & Autopsy
date: 2022-12-21 18:32:00 -0500
categories: [Walkthrough, Tryhackme, CTF]
tags: [linux, security, disk analysis]
---

<https://tryhackme.com/room/autopsy2ze0> Ready for a challenge? Use Autopsy to investigate artifacts from a disk image.

# What is the MD5 hash of the E01 image?

<img src="/assets/disk-analysis01.png" alt="Disk Analysis">

# What is the computer Account name?

We generate a report to find some of this information. the computer account name is inside Operating System Information in the report.

# List all the user accounts. (alphabetical order)

We can find these in the report under operating system user accounts.

joshwa,keshav,sandhya,shreya,sivapriya,srini,suba	

# Who was the last user to log into the computer?

in the same location we can see the last date accessed. sivapriya @ 	2021-02-07 12:05:37 EST

# What was the IP address of the computer?

Look@LAN in programfiles irunin.ini hold the LAN ip.

# What is the MAC address of the computer?

In look@lan see LANNIC - 0800272cc4b9

# What is the name of the network card on this computer?

Select operating system information > double click system > browse to Root/MNicrosoft/Windows NT/CurrentVers/NetworksCards/2

# What is the name of the network monitoring tool?

Look@LAN

# A user bookmarked a Google Maps location. What are the coordinates of the location?

check web nbookmarks. 12°52'23.0"N 80°13'25.0"E - Google Maps

# A user has his full name printed on his desktop wallpaper. What is the user's full name?

Anto Joshwa Check Image/video to browse images

# A user had a file on her desktop. It had a flag but she changed the flag using PowerShell. What was the first flag?

check powershell history for users. HASAN2/vol3/users/shreya/AppData/roaming/microsoft/windows/pwoershell/psreadline/consolehost_history.txt

# The same user found an exploit to escalate privileges on the computer. What was the message to the device owner?

we can find the exploit on her desktop

# 2 hack tools focused on passwords were found in the system. What are the names of these tools? (alphabetical order)

Check Windows defender

Data Sources > HASAN2.E01 > Vol3 > Program Data > Microsoft > Windows Defender > Scans > History > Service > DetectionHistory > 02

# There is a YARA file on the computer. Inspect the file. What is the name of the author?

The YAR file in hidden inside mimikatz_trunk 

# One of the users wanted to exploit a domain controller with an MS-NRPC based exploit. What is the filename of the archive that you found? (include the spaces in your answer)

The answer is the zerologin encrypted.zip inside recent documents that belongs to sandhya


