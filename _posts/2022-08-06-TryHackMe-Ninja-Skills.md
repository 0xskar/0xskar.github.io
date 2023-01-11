---
title: Walkthrough - Ninja Skills
published: true
---

Linux, Basics, CTF. Practice linux skills and complete challenges.

![0xskar](/assets/ninja-skills01.png)

[https://tryhackme.com/room/ninjaskills](https://tryhackme.com/room/ninjaskills)

* * *

## Synopsis

Answer the questions about the following files:

```
    8V2L
    bny0
    c4ZX
    D8B3
    FHl1
    oiMO
    PFbD
    rmfX
    SRSq
    uqyw
    v2Vb
    X1Uy
```

* * * 

## Which of the above files are owned by the best-group group(enter the answer separated by spaces in alphabetical order)

- ``cat /etc/group``
- ``find / -group 502 2>/dev/null``

* * * 

## Which of these files contain an IP address?

- ``find / -type f \( -name 8V2l -o -name bny0 -o -name c4ZX -o -name D8B3 -o -name FHl1 -o -name oiMO -o -name PFbD -o -name rmfX -o -name SRSq -o -name uqyw -o -name v2Vb -o -name X1Uy \) -exec grep -E -o "(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)" {} * \; 2>/dev/null``

* * * 

## Which file has the SHA1 hash of 9d54da7584015647ba052173b84d45e8007eba94

- ``for f in *; do echo '9d54da7584015647ba052173b84d45e8007eba94 '$f | sha1sum -c; done | grep OK``

* * * 

## Which file contains 230 lines?

- ``find / -type f \( -name 8V2l -o -name bny0 -o -name c4ZX -o -name D8B3 -o -name FHl1 -o -name oiMO -o -name PFbD -o -name rmfX -o -name SRSq -o -name uqyw -o -name v2Vb -o -name X1Uy \) -exec wc -l 230 {} \; 2>/dev/null``

* * * 

## Which file's owner has an ID of 502?

- ``find / -type f \( -name 8V2l -o -name bny0 -o -name c4ZX -o -name D8B3 -o -name FHl1 -o -name oiMO -o -name PFbD -o -name rmfX -o -name SRSq -o -name uqyw -o -name v2Vb -o -name X1Uy \) -exec ls -ln {} \; 2>/dev/null``

* * * 

## Which file is executable by everyone?

- 8V2L

* * * 