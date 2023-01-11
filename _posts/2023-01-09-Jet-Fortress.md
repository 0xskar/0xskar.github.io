---
title: Jet
date: 2023-01-09 18:32:00 -0500
categories: [Walkthrough, Hackthebox, Fortress]
tags: [linux, nmap]
---

- nmap to discover services

```
PORT     STATE SERVICE     REASON
22/tcp   open  ssh         syn-ack ttl 63
53/tcp   open  domain      syn-ack ttl 63
80/tcp   open  http        syn-ack ttl 63
5555/tcp open  freeciv     syn-ack ttl 63
7777/tcp open  cbt         syn-ack ttl 63
9201/tcp open  wap-wsp-wtp syn-ack ttl 63
9989/tcp open  unknown     syn-ack ttl 63
9999/tcp open  abyss       syn-ack ttl 63
```

- `nmap -sC -sV -O secondary nmap scan`

s.