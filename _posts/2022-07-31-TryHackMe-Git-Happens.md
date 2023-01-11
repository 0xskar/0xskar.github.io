---

title: Walkthrough - Git Happens
published: true
---

Security, Git, Web. Boss wanted me to create a prototype, so here it is! We even used something called "version control" that made deploying this really easy!

[https://tryhackme.com/room/githappens](https://tryhackme.com/room/githappens)

* * *

## Notes

The initial nmap scans only show one open port a webserver. Super Awesome Site, isnt very super awesome. It has a script or something to do with the login we have to figure out capturing in burpsuite make this easiar to decipher.

Secondary nmap scan ``nmap -sV -sT -sC -p80 10.10.5.11`` we find a git repository.

- using GitTools

- Dump the git repository from the target: ``~/scripts/GitTools/Dumper/gitdumper.sh http://10.10.5.11/.git/ git-happens``
- Extract the git repository ``~/scripts/GitTools/Extractor/extractor.sh git-happens/ git-happens-extracted``

- We can then browse the extracted git repository to find our super secret password.

* * * 

## Super secret password

- ``cat index.html``

![0xskar](/assets/git-happens.png)

* * * 

