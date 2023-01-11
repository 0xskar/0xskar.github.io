---
title: Active Reconnisance
date: 2022-06-15 18:32:00 -0500
categories: [Lesson, Tutorial]
tags: [Enumeration, TryHackMe]
---

Learn how to use simple tools such as traceroute, ping, telnet, and a web browser to gather information.

[https://tryhackme.com/room/activerecon](https://tryhackme.com/room/activerecon)

* * *

## Task 1 - Introduction

Focus on active reconnaissance and the essential tools related to it. We learn to use a web browser to collect more information about our target. Moreover, we discuss using simple tools such as ping, traceroute, telnet, and nc to gather information about the network, system, and services.

Active reconnaissance requires you to make some kind of contact with your target. This contact can be a phone call or a visit to the target company under some pretence to gather more information, usually as part of social engineering. Alternatively, it can be a direct connection to the target system, whether visiting their website or checking if their firewall has an SSH port open. 

* * * 

## Task 2 - Web Browser 

Developer tools are useful, get familiar with them. CTRL+SHIFT+I

**Browse to the following website and ensure that you have opened your Developer Tools on AttackBox Firefox, or the browser on your computer. Using the Developer Tools, figure out the total number of questions.** 

There are 8 Questions we can see in the debugger.


* * * 

## Task 3 - Ping

In simple terms, the ping command sends a packet to a remote system, and the remote system replies. This way, you can conclude that the remote system is online and that the network is working between the two systems.

Generally speaking, when we don’t get a ping reply back, there are a few explanations that would explain why we didn’t get a ping reply, for example:

   - The destination computer is not responsive; possibly still booting up or turned off, or the OS has crashed.
   - It is unplugged from the network, or there is a faulty network device across the path.
   - A firewall is configured to block such packets. The firewall might be a piece of software running on the system itself or a separate network appliance. Note that MS Windows firewall blocks ping by default.
   - Your system is unplugged from the network.

**Which option would you use to set the size of the data carried by the ICMP echo request?** -s

**What is the size of the ICMP header in bytes?** 8

**Does MS Windows Firewall block ping by default? (Y/N)** y

**Deploy the VM for this task and using the AttackBox terminal, issue the command ping -c 10 MACHINE_IP. How many ping replies did you get back?** 10

* * * 

## Task 4 - Traceroute 

   - The number of hops/routers between your system and the target system depends on the time you are running traceroute. There is no guarantee that your packets will always follow the same route, even if you are on the same network or you repeat the traceroute command within a short time.
   - Some routers return a public IP address. You might examine a few of these routers based on the scope of the intended penetration testing.
   - Some routers don’t return a reply.


**In Traceroute A, what is the IP address of the last router/hop before reaching tryhackme.com?** 172.67.69.208

**In Traceroute B, what is the IP address of the last router/hop before reaching tryhackme.com?** 104.26.11.229

**In Traceroute B, how many routers are between the two systems?** 26

**Start the attached VM from Task 3 if it is not already started. On the AttackBox, run traceroute 10.10.208.84. Check how many routers/hops are there between the AttackBox and the target VM.** 

* * *

## Task 5 - Telnet 

``telnet [ip-address] [port]``

**Start the attached VM from Task 3 if it is not already started. On the AttackBox, open the terminal and use the telnet client to connect to the VM on port 80. What is the name of the running server?** Apache

**What is the version of the running server (on port 80 of the VM)?** 2.4.10

* * * 

## Task 6 - Netcat

We know about netcat but anyways heres some options:

| option | meaning |
|--------|---------|
| -l | Listen mode |
| -p | Specify the Port number |
| -n | Numeric only; no resolution of hostnames via DNS |
| -v | Verbose output (optional, yet useful to discover any bugs) |
| -vv | Very Verbose (optional) |
| -k | Keep listening after client disconnects |

* * * 






