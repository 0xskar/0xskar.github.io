---
title: Upgrading Netcat Shell
published: false
---

Tags: Linux, shell.
Description: How to upgrade simple netcat shell to full TTY
Difficulty: Easy

* * *

## Notes

I've had many issues with many types of reverse shells using socat or pwncat or pretty much everything with lines overlapping and other things so here are simple steps to upgrade normal netcat shells.

1. `nc -nvlp 6666`
2. `python3 -c 'import pty;pty.spawn("/bin/bash")'`
3. Control+Z to backgroud once have a connection.
4. `echo $TERM` term type
5. `stty -a` gives us the size of the current TTY
6. `stty raw -echo && fg`
7. now connect back on target type `reset`

```
# In reverse shell
$ python -c 'import pty; pty.spawn("/bin/bash")'
Ctrl-Z

# In Kali
$ stty raw -echo
$ fg

# In reverse shell
$ reset
$ export SHELL=bash
$ export TERM=xterm-256color
$ stty rows <num> columns <cols>
```

* * * 
 

