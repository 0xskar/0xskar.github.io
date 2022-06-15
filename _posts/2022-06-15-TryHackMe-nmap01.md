---
title: TryHackMe - Nmap Live Host Discovery
published: true
---

[https://tryhackme.com/room/nmap01](https://tryhackme.com/room/nmap01)

Learn how to use Nmap to discover live hosts using ARP scan, ICMP scan, and TCP/UDP ping scan.

* * *

## [](#header-2)Task 1 - Introduction

When we want to target a network, we want to find an efficient tool to help us handle repetitive tasks and answer the following questions:

   - Which systems are up?
   - What services are running on these systems?

* * * 

## [](#header-2)Task 2 - Subnetworks 

Send a packet with the following:

   - From computer1
   - To computer1 (to indicate it is broadcast)
   - Packet Type: “ARP Request”
   - Data: computer6 (because we are asking for computer6 MAC address using ARP Request)

**How many devices can see the ARP Request?** 4

**Did computer6 receive the ARP Request? (Y/N)** n

Send a packet with the following:

   - From computer4
   - To computer4 (to indicate it is broadcast)
   - Packet Type: “ARP Request”
   - Data: computer6 (because we are asking for computer6 MAC address using ARP Request)

**How many devices can see the ARP Request?** 4

**Did computer6 reply to the ARP Request? (Y/N)** y

* * * 

## [](#header-2)Task 3 Enumerating Targets 

**What is the first IP address Nmap would scan if you provided 10.10.12.13/29 as your target?** 

``nmap -sL -n 10.10.12.13/29`` 10.10.12.8

**How many IP addresses will Nmap scan if you provide the following range 10.10.0-255.101-125?** 

``nmap -sL -n 10.10.0-255.101-125`` 6400

* * * 
