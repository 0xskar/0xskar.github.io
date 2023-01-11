---
title: Walkthrough - Confidential
published: true
---

Tags: Security, Forensics, PDF, QR.
Description: We got our hands on a confidential case file from some self-declared "black hat hackers"... it looks like they have a secret invite code.
Difficulty: Easy
URL: [https://tryhackme.com/room/confidential](https://tryhackme.com/room/confidential)

* * *

## Notes

- using `pdfimages Repdf.pdf` we can extract the PDF into 3 images. with `.000.ppm` showing the origional PDF. Now how to we read the QR? We can do so with `zbarimg`. so `zbarimg .-000.ppm` gives us the flag.

* * * 

## Uncover and scan the QR code to retrieve the flag.

![0xskar](/assets/confidential01.png)

* * * 

