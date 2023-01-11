---
title: Walkthrough - Committed
published: True
---

Tags: Security, CTF, GIT, Source Code.
Description: One of our developers accidentally committed some sensitive code to our GitHub repository. Well, at least, that is what they told us...
Difficulty: Easy
URL: [https://tryhackme.com/room/committed](https://tryhackme.com/room/committed)

* * *

## Notes

> Oh no, not again! One of our developers accidentally committed some sensitive code to our GitHub repository. Well, at least, that is what they told us... the problem is, we don't remember what or where! Can you track down what we accidentally committed?

CHECKING LOGS. pretty sure found the commit that they're looking for, now how to access the commit history to find it.

- `git log --all`

- commit c56c470a2a9dfb5cfbd54cd614a9fdb1644412b5 seems to be the oops?

- from the root dir `git checkout c56c470a2a9dfb5cfbd54cd614a9fdb1644412b5`

- `git show c56c470a2a9dfb5cfbd54cd614a9fdb1644412b5` gives us our flag


* * * 

## Discover the flag in the repo

![0xskar](/assets/committed01.png)

* * * 

