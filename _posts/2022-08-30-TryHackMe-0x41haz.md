---
title: Walkthrough - 0x41haz
published: true
---

Tags: Security, Reversing, binex, radare.
Description: Simple Reversing Challenge
Difficulty: Easy
URL: [https://tryhackme.com/room/0x41haz](https://tryhackme.com/room/0x41haz)

* * *

## Find the password

> In this challenge, you are asked to solve a simple reversing solution. Download and analyze the binary to discover the password.

> There may be anti-reversing measures in place!

Download task files. Trying to open in ghidra we have an unknown binanry checking `file` tells us this as well. Editing the 6th byte to 01 fixes this. Seem to be unable to edit the file in ghidra but using  `r2 0x41haz.0x41haz` `aaa` `s main` `pdf` and looking through the file we find the pass.

![0xskar](/assets/0x41hax01.png)

Entering this into the program gives shows us this is the correct password.
* * * 

