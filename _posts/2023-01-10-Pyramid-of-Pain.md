---
title: Pyramid of Pain
date: 2023-01-09 18:32:00 -0500
categories: [Lesson, Tryhackme]
tags: [SOC, SOC Analyst, Threat Hunting, Hashes]
---

![Pyramid of Pain](/assets/pop2.png)

Learn what is the Pyramid of Pain and how to utilize this model to determine the level of difficulty it will cause for an adversary to change the indicators associated with them, and their campaign. 

# Hash Values (Trivial)

A hash is the result of a hashing algorithm. A hash value is a unique value that identifies data. Some of the most common hashing algorithms are:

- **MD5 (Message Digest)** - Designed in 1992 and is a widely used cryptographic hash function with a 128-bit hash. **NOT** considered cryptographically secure. Vulnerable against a number of attacks including hash collision. Hash collision is when two different input values create the same hash. This can be used by an attacker to create a rogue version of the hash and use that hash to gain access to the legitimate file with the same MD5 hash.
- **SHA-1 (Secure Hashing Algorithm 1)** - Was designed by the bad guys at the NSA. of couse they build backdoors into algorithm just like the CIA did. It's not secure and banned for its use now because it's succeptable to brute force attacks.
- **SHA-2 (Secure Hashing Algorihm 2)** - 








Source(s): <https://tryhackme.com/room/pyramidofpainax> 