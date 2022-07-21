---
title: Walkthrough - Investigating Windows
published: true
---

A windows machine has been hacked, its your job to go investigate this windows machine and find clues to what the hacker might have done.

[https://tryhackme.com/room/investigatingwindows](https://tryhackme.com/room/investigatingwindows)

* * *

## [](#header-2)Whats the version and year of the windows machine?

- ``Get-Computerinfo``

* * *

## [](#header-2)Which user logged in last?

- ``Get-LocalUser | Format-List -Property Name,LastLogon``

* * *

## [](#header-2)When did John log onto the system last? Answer format: MM/DD/YYYY H:MM:SS AM/PM

- ``Get-LocalUser | Format-List -Property Name,LastLogon``
- Format is weird have to add 0's infront of the dates for the answer to work on tryhackme.

* * *

## [](#header-2)What IP does the system connect to when it first starts?

- Reconnect to the RDP and get 10.34.2.3

* * *

## [](#header-2)What two accounts had administrative privileges (other than the Administrator user)? Answer format: username1, username2

- ``Get-LocalGroupMember -Name Administrators``

* * *

## [](#header-2)Whats the name of the scheduled task that is malicous.

- Get-ScheduledTask | Where-Object -Property State -EQ Ready
- Can also view task scheduler

* * *

## [](#header-2)What file was the task trying to run daily?

- Check Windows Task Scheduler Actions these program is trying to run a netcat listener powershell script

* * *

## [](#header-2)What port did this file listen locally for?

- See above

* * *

## [](#header-2)When did Jenny last logon?

- ``Get-LocalUser Jenny | Format-List`` - Never

* * *

## [](#header-2)At what date did the compromise take place? Answer format: MM/DD/YYYY

- Event Viewer - 3/2/2019 4:02:58 PM 

* * *

## [](#header-2)At what time did Windows first assign special privileges to a new logon? Answer format: MM/DD/YYYY HH:MM:SS AM/PM

- Audit Success   3/2/2019 4:04:49 PM Microsoft Windows security auditing.    4672    Special Logon

* * *

## [](#header-2)What tool was used to get Windows passwords?

- mimikatz - because it's been popping up and interrupting us scanning events for the past hour

* * *

## [](#header-2)What was the attackers external control and command servers IP?

- Checking the hosts file in ``C:\Windows\System32\drivers\etc``

* * *

## [](#header-2)What was the extension name of the shell uploaded via the servers website?

- ``C:\inetpub\www``

* * *

## [](#header-2)What was the last port the attacker opened?

- windows firewall 1337

* * *

## [](#header-2)Check for DNS poisoning, what site was targeted?

- hosts file again

* * * 