---
title: Walkthrough - Volatility With Volatility3
published: true
---

Tags: Memory Forensics.
Description: Learn how to perform memory forensics with Volatility!
Difficulty: Easy
URL: [https://tryhackme.com/room/bpvolatility](https://tryhackme.com/room/bpvolatility)

* * *

## Obtaining Memory Samples

-    FTK Imager - Link
-    Redline - Link *Requires registration but Redline has a very nice GUI
-    DumpIt.exe
-    win32dd.exe / win64dd.exe - Has fantastic psexec support, great for IT departments if your EDR solution doesn't support this

These tools will typically output a .raw file which contains an image of the system memory. The .raw format is one of the most common memory file types you will see in the wild.

hiberfil.sys, better known as the Windows hibernation file contains a compressed memory image from the previous boot. Microsoft Windows systems use this in order to provide faster boot-up times, however, we can use this file in our case for some memory forensics!


Things get even more exciting when we start to talk about virtual machines and memory captures. Here's a quick sampling of the memory capture process/file containing a memory image for different virtual machine hypervisors:

-    VMware - .vmem file
-    Hyper-V - .bin file
-    Parallels - .mem file
-    VirtualBox - .sav file This is only a partial memory file. You'll need to dump memory like a normal bare-metal system for this hypervisor

These files can often be found simply in the data store of the corresponding hypervisor and often can be simply copied without shutting the associated virtual machine off. This allows for virtually zero disturbance to the virtual machine, preserving it's forensic integrity.

**What memory format is the most common?**

- `.raw`

**The Window's system we're looking to perform memory forensics on was turned off by mistake. What file contains a compressed memory image?**

- `hiberfil.sys`

**How about if we wanted to perform memory forensics on a VMware-based virtual machine?**

- `.vmem`

* * * 

## Examining Our Patient 

First, let's figure out what profile we need to use. Profiles determine how Volatility treats our memory image since every version of Windows is a little bit different. Let's see our options now with the command `volatility -f MEMORY_FILE.raw imageinfo`

- `sudo vol -f cridex.vmem windows.info.Info`

```
Variable        Value

Kernel Base     0x804d7000
DTB     0x2fe000
Symbols file:///usr/local/lib/python3.10/dist-packages/volatility3-1.0.0-py3.10.egg/volatility3/symbols/windows/ntkrnlpa.pdb/30B5FB31AE7E4ACAABA750AA241FF331-1.json.xz
Is64Bit False
IsPAE   True
primary 0 WindowsIntelPAE
memory_layer    1 FileLayer
KdDebuggerDataBlock     0x80545ae0
NTBuildLab      2600.xpsp.080413-2111
CSDVersion      3
KdVersionBlock  0x80545ab8
Major/Minor     15.2600
MachineType     332
KeNumberProcessors      1
SystemTime      2012-07-22 02:45:08
NtSystemRoot    C:\WINDOWS
NtProductType   NtProductWinNt
NtMajorVersion  5
NtMinorVersion  1
PE MajorOperatingSystemVersion  5
PE MinorOperatingSystemVersion  1
PE Machine      332
PE TimeDateStamp        Sun Apr 13 18:31:06 2008
```

**Running the imageinfo command in Volatility will provide us with a number of profiles we can test with, however, only one will be correct. We can test these profiles using the pslist command, validating our profile selection by the sheer number of returned results. Do this now with the command `volatility -f MEMORY_FILE.raw --profile=PROFILE pslist`. What profile is correct for this memory image?**

- since we are using volatility 3 it is simplified and doesnt use profiles

**Take a look through the processes within our image. What is the process ID for the smss.exe process? If results are scrolling off-screen, try piping your output into less**

- `sudo vol -f cridex.vmem windows.pslist | more`
- `368`

**In addition to viewing active processes, we can also view active network connections at the time of image creation! Let's do this now with the command `volatility -f MEMORY_FILE.raw --profile=PROFILE netscan`. Unfortunately, something not great is going to happen here due to the sheer age of the target operating system as the command netscan doesn't support it.**

- `no answer needed because the windows version is unsupported`

**It's fairly common for malware to attempt to hide itself and the process associated with it. That being said, we can view intentionally hidden processes via the command `psxview`. What process has only one 'False' listed?**

- had an issue with this as well volatility 3 doesnt have a psxview equivalent.
- `python2.7 vol.py -f ~/Documents/TryHackMe/Volatility/cridex.vmem --profile=WinXPSP2x86 psxview`

**In addition to viewing hidden processes via psxview, we can also check this with a greater focus via the command 'ldrmodules'. Three columns will appear here in the middle, InLoad, InInit, InMem. If any of these are false, that module has likely been injected which is a really bad thing. On a normal system the grep statement above should return no output. Which process has all three columns listed as 'False' (other than System)?**

- `python2.7 vol.py -f ~/Documents/TryHackMe/Volatility/cridex.vmem --profile=WinXPSP2x86 ldrmodules | grep False`

**Injected code can be a huge issue and is highly indicative of very very bad things. We can check for this with the command `malfind`. Using the full command `volatility -f MEMORY_FILE.raw --profile=PROFILE malfind -D <Destination Directory>` we can not only find this code, but also dump it to our specified directory. Let's do this now! We'll use this dump later for more analysis. How many files does this generate?**

- `12`

**Last but certainly not least we can view all of the DLLs loaded into memory. DLLs are shared system libraries utilized in system processes. These are commonly subjected to hijacking and other side-loading attacks, making them a key target for forensics. Let's list all of the DLLs in memory now with the command `dlllist`**

- `python2.7 vol.py -f ~/Documents/TryHackMe/Volatility/cridex.vmem --profile=WinXPSP2x86 dlllist`

**Now that we've seen all of the DLLs running in memory, let's go a step further and pull them out! Do this now with the command `volatility -f MEMORY_FILE.raw --profile=PROFILE --pid=PID dlldump -D <Destination Directory>` where the PID is the process ID of the infected process we identified earlier (questions five and six). How many DLLs does this end up pulling?**

- `python2.7 vol.py -f ~/Documents/TryHackMe/Volatility/cridex.vmem --profile=WinXPSP2x86 --pid=584 dlldump -D ~/Documents/TryHackMe/Volatility`

* * * 

