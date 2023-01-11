---
title: Walkthrough - Dig Dug
published: true
---

Tags: Security, DNS, Dig, CTF.
Description: Turns out this machine is a DNS server - it's time to get your shovels out!
Difficulty: Easy
URL: [https://tryhackme.com/room/digdug](https://tryhackme.com/room/digdug)

* * *

## Notes

> Oooh, turns out, this 10.10.52.67 machine is also a DNS server! If we could dig into it, I am sure we could find some interesting records! But... it seems weird, this only responds to a special type of request for a givemetheflag.com

- `dig A @10.10.52.67 givemetheflag.com`

lmao

* * * 