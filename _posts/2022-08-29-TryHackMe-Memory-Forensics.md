---
title: Walkthrough - Memory Forensics
published: true
---

Tags: Forensics, Memory, Volatility, Security.
Description: Perform memory forensics to find the flags.
Difficulty: Easy
URL: [https://tryhackme.com/room/memoryforensics](https://tryhackme.com/room/memoryforensics)

* * *

## Login

**The forensic investigator on-site has performed the initial forensic analysis of John's computer and handed you the memory dump he generated on the computer. As the secondary forensic investigator, it is up to you to find all the required information in the memory dump.**

- `python2.7 ~/scripts/volatility-master/vol.py -f Snapshot6.vmem imageinfo`

```
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : Win7SP1x64, Win7SP0x64, Win2008R2SP0x64, Win2008R2SP1x64_23418, Win2008R2SP1x64, Win7SP1x64_23418
                     AS Layer1 : WindowsAMD64PagedMemory (Kernel AS)
                     AS Layer2 : FileAddressSpace (/home/oskar/Documents/TryHackMe/MemoryForensics/Snapshot6.vmem)
                      PAE type : No PAE
                           DTB : 0x187000L
                          KDBG : 0xf80002c4a0a0L
          Number of Processors : 1
     Image Type (Service Pack) : 1
                KPCR for CPU 0 : 0xfffff80002c4bd00L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2020-12-27 06:20:05 UTC+0000
     Image local date and time : 2020-12-26 22:20:05 -0800
```

- Dump the hashes with hivelist `python2.7 ~/scripts/volatility-master/vol.py -f Snapshot6.vmem --profile Win7SP1x64 hivelist`

```
irtual            Physical           Name
------------------ ------------------ ----
0xfffff8a001453010 0x000000003b039010 \??\C:\Users\John\AppData\Local\Microsoft\Windows\UsrClass.dat
0xfffff8a00000f010 0x0000000027324010 [no name]
0xfffff8a000024010 0x00000000271af010 \REGISTRY\MACHINE\SYSTEM
0xfffff8a000061010 0x00000000272ee010 \REGISTRY\MACHINE\HARDWARE
0xfffff8a000790010 0x00000000211b5010 \Device\HarddiskVolume1\Boot\BCD
0xfffff8a0007f1010 0x0000000021368010 \SystemRoot\System32\Config\SOFTWARE
0xfffff8a000a8e010 0x000000001b1e8010 \SystemRoot\System32\Config\DEFAULT
0xfffff8a000cce010 0x00000000172b1010 \SystemRoot\System32\Config\SECURITY
0xfffff8a000cf8010 0x0000000016ce6010 \SystemRoot\System32\Config\SAM
0xfffff8a000d81010 0x00000000162d5010 \??\C:\Windows\ServiceProfiles\NetworkService\NTUSER.DAT
0xfffff8a000e0e010 0x0000000016073010 \??\C:\Windows\ServiceProfiles\LocalService\NTUSER.DAT
0xfffff8a0013ee010 0x000000003bc0d010 \??\C:\Users\John\ntuser.dat
```

- With the virtual offset of SYSTEM and SAM we can extract the hashes I couldnt get this to work with volatility 2.7 so using volatility 3 for this
- `sudo vol -f Snapshot6.vmem hashdump`

```
User    rid     lmhash  nthash

Administrator   500     aad3b435b51404eeaad3b435b51404ee        31d6cfe0d16ae931b73c59d7e0c089c0
Guest   501     aad3b435b51404eeaad3b435b51404ee        31d6cfe0d16ae931b73c59d7e0c089c0
John    1001    aad3b435b51404eeaad3b435b51404ee        47fbd6536d7868c873d5ea455f2fc0c9
HomeGroupUser$  1002    aad3b435b51404eeaad3b435b51404ee        91c34c06b7988e216c3bfeb9530cabfb
```

- `hashcat -m 1000 john.hash /usr/share/seclists/Passwords/rockyou.txt`

* * * 

## Analysis

> On arrival a picture was taken of the suspect's machine, on it, you could see that John had a command prompt window open. The picture wasn't very clear, sadly, and you could not see what John was doing in the command prompt window.

> To complete your forensic timeline, you should also have a look at what other information you can find, when was the last time John turned off his computer?

> Answer the questions below

**When was the machine last shutdown?**

- `sudo python2.7 ~/scripts/volatility-master/vol.py -f Snapshot19.vmem imageinfo`
- volatility doesnt work
- `2020-12-27 22:50:12`

**What did John write?**

- `forgetmenot`

* * * 
