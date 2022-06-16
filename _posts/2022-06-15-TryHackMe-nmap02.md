---
title: TryHackMe - Nmap Basic Port Scans
published: true
---

[https://tryhackme.com/room/nmap02](https://tryhackme.com/room/nmap02)

Learn in-depth how nmap TCP connect scan, TCP SYN port scan, and UDP port scan work.

* * *

## [](#header-2)Task 1 - Introduction

This room and the next one, we focus on port scanning and the different types of port scans used by nmap. This room explains:

   - TCP connect port scan
   - TCP SYN port scan
   - UDP port scan

Moreover, we discuss the different options to specify the ports, the scan rate, and the number of parallel probes.

* * * 

## [](#header-2)Task 2 - TCP and UDP Ports

Oversimplified, we can classify ports in two states:

   1. Open port indicates that there is some service listening on that port.
   2. Closed port indicates that there is no service listening on that port.

 In practical situations, we need to consider the impact of firewalls. For instance, a port might be open, but a firewall might be blocking the packets. Therefore, Nmap considers the following six states:

   1. **Open**: indicates that a service is listening on the specified port.
   2. **Closed**: indicates that no service is listening on the specified port, although the port is accessible. By accessible, we mean that it is reachable and is not blocked by a firewall or other security appliances/programs.
   3. **Filtered**: means that Nmap cannot determine if the port is open or closed because the port is not accessible. This state is usually due to a firewall preventing Nmap from reaching that port. Nmap’s packets may be blocked from reaching the port; alternatively, the responses are blocked from reaching Nmap’s host.
   4. **Unfiltered**: means that Nmap cannot determine if the port is open or closed, although the port is accessible. This state is encountered when using an ACK scan ``-sA``.
   5. **Open|Filtered**: This means that Nmap cannot determine whether the port is open or filtered.
   6. **Closed|Filtered**: This means that Nmap cannot decide whether a port is closed or filtered.

##### [](#header-5)Answer the questions below

**Which service uses UDP port 53 by default?** DNS

**Which service uses TCP port 22 by default?** SSH

**How many port states does Nmap consider?** 6

**Which port state is the most interesting to discover as a pentester?** OPEN!










