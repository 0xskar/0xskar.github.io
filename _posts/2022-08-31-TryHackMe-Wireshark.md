---
title: Walkthrough - Plotted-TMS
published: true
---

Tags: Wireshark.
Description: basics of Wireshark and how to analyze various protocols and PCAPs
Difficulty: Easy
URL: [https://tryhackme.com/room/wireshark](https://tryhackme.com/room/wireshark)

* * *

## Filtering Operators

Wireshark's filter syntax can be simple to understand making it easy to get a hold of quickly. To get the most out of these filters you need to have a basic understanding of boolean and logic operators.

Wireshark only has a few that you will need to be familiar with:

   - and - operator: and / &&
   - or - operator: or / ||
   - equals - operator: eq / ==
   - not equal - operator: ne / !=
   - greater than - operator: gt /  >
   - less than - operator: lt / <

See also: [Wireshark Filtering Documentation](https://www.wireshark.org/docs/wsug_html_chunked/ChWorkBuildDisplayFilterSection.html)

* * * 

## ARP Traffic

ARP or Address Resolution Protocol is a Layer 2 protocol that is used to connect IP Addresses with MAC Addresses. 

**What is the Opcode for Packet 6?**

- `request (1)`

**What is the source MAC Address of Packet 19?**

- `80:fb:06:f0:45:d7`

**What 4 packets are Reply packets?**

- `76,400,459,520`

**What IP Address is at 80:fb:06:f0:45:d7?**

- `10.251.23.1`

* * * 

## ICMP Traffic 

ICMP or Internet Control Message Protocol is used to analyze various nodes on a network. This is most commonly used with utilities like ping and traceroute. You should already be familiar with how ICMP works; however, if you need a refresher, [read the IETF documentation](https://tools.ietf.org/html/rfc792).

**What is the type for packet 4?**

- 8

**What is the type for packet 5?**

- 0

**What is the timestamp for packet 12, only including month day and year?**
note: Wireshark bases it’s time off of your devices time zone, if your answer is wrong try one day more or less. 

- `May 30, 2013`

**What is the full data string for packet 18?**

- `08090a0b0c0d0e0f101112131415161718191a1b1c1d1e1f202122232425262728292a2b2c2d2e2f3031323334353637`

* * * 

## TCP Traffic

TCP or Transmission Control Protocol handles the delivery of packets including sequencing and errors. You should already have an understanding of how TCP works, if you need a refresher check out the [IETF TCP Documentation](https://tools.ietf.org/html/rfc793).

* * * 

## DNS Traffic

DNS or Domain Name Service protocol is used to resolves names with IP addresses. Just like the other protocols, you should be familiar with DNS; however, if you're not you can refresh with the IETF DNS Documentation. 

There are a couple of things outlined below that you should keep in the back of your mind when analyzing DNS packets.

  -  Query-Response
  -  DNS-Servers Only
  -  UDP

If anyone of these is out of place then the packets should be looked at further and should be considered suspicious.

* * * 