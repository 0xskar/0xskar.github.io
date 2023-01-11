---
title: Enumeration 
published: true
---

* * *

## Linux Enumeration

This section will go over commands and information on four categories we can acquire post exploitation. 

- System
- Users
- Networking
- Running Services

##   System

We can find more information about the Linux Distribution and release version by searching for files that end with `-release` in `/etc/` - `ls /etc/*-release`

```
┌──(0xskar㉿cocokali)-[~]
└─$ cat /etc/os-release                                                                                     
PRETTY_NAME="Kali GNU/Linux Rolling"
NAME="Kali GNU/Linux"
ID=kali
VERSION="2022.3"
VERSION_ID="2022.3"
VERSION_CODENAME="kali-rolling"
ID_LIKE=debian
ANSI_COLOR="1;31"
HOME_URL="https://www.kali.org/"
SUPPORT_URL="https://forums.kali.org/"
BUG_REPORT_URL="https://bugs.kali.org/"
```

We can find machines name with `hostname`

Checking various files on the system for useful information. `/etc/passwd/`, `/etc/group`, `/etc/shadow`

Other directories can also reveal infornmation about users and contain sensitive information, such as the mail directories found at `/var/mail/`

We can also check for installed applications by checking the files in `/usr/bin`, and `/sbin/`. 

For RPM-based linux systems we can check installed packages with `rpm -qa`, `-qa` flag meaning query all

For Debian-based systems we can check with `dpkg -l`

##   Users

By checking `/etc/passwd` we can see usernames, but other commands can also help us get more information about users on the system. Checking `who` lets us see logged in users.

```
┌──(0xskar㉿cocokali)-[~]
└─$ who                  
0xskar   tty7         2022-10-25 00:17 (:0)
0xskar   pts/0        2022-10-25 00:17 (:0)
```

`w` will show us who is logged in and what they're doing.

```
┌──(0xskar㉿cocokali)-[~]
└─$ w  
 03:50:11 up 11:07,  2 users,  load average: 0.17, 0.26, 0.26
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
0xskar   tty7     :0               Tue00    3days 11:32   7.95s xfce4-session
0xskar   pts/0    :0               Tue00    1.00s 28.22s  0.01s w
```

`id` will print our users group and user ID. `last` will display a list of the last logged-in users, and how long they stayed connected. And `sudo -l` will let us see which sudo commands our user has access to.

##   Networking

`ip a s` will show us our ip address information

DNS servers can be found in the `/etc/resolv.conf`

`netstat` is a useful command for learning about network connections, routing tables, and interface statistics. many different useful flags. `-a` shows listening and non-listening sockets (all sockets), `-l` only listening sockets, `-n` numeric output instead of resolving the ip addr and port num. `-t` for TCP `-u` for UDP, `-x` for UNIX, and `-p` for the PID and name of the program it belongs to. Using `nmap` post exploitation is generally fine for finding some access points and enumeration, but firewalls and IDS and IPS systems can also sense the packets we are sending while trying to find the access points with nmap and drop the packets, resulting in incomplete nmap results, therefor netstat is a useful command for us to run post exploitation.

`lsof` stands for List Open Files. We can use `lsof -i` if we want to display only internet and network connections, to get a comple4ist we would need to run this as `sudo`. If there is a long list it can be limited by adding the port as a flag `:25` for example for SMTP. `lsof -i :25`.

##   Running Services

We can get list of processes with `ps`. Many useful flags `-e` will list every process on the system. `-f` will list full information, `-l` for long format. We can get a comparable output with `ps -aux`. `ps axfj` will print a process tree as well showing process hierarchy.

* * * 

## Windows Enumeration

Say we have gained access to a windows machine through an exploit. We now have a shell and have run `powershell.exe`. 

One command that can give us detailed information about the system is `systeminfo`, which will show us a lot such as OS name, version, hotfixes installed, and more.

We can check installed updates with `wmic qfe get Caption,Description`. This will let us know how often or quickly the system is being patched and updated.

We can check installed and started Windows services with `net start`

If we only want to see installed apps we can use `wmic product get name,version,vendor`. 

##   Users

`whoami` will let us know what user we are on while `whoami /priv` will show us what our user is capable of. `whoami /groups` shows us our users groups. `net user` shows us all users on the system. 

`net group` wiull show us available groups if the system is part of a Windows Domain Controller else `net localgroup` if is it not part of a controller. `net localgroup administrators` gives us a list of users who are members of the administrator group.

`net accounts` will show us local machine settings, or `net accounts /domain` if the machine is part of a domain controller. This command lets us learn the password policy.

##   Networking

`ipcoinfig` to show us the system network configuration. `ipconfig /all` if we want to know all network related information.

`netstat` to show active connections. `-h` for more flags.

* * * 

## DNS

Depending on the DNS server configuration, DNS zone transfer might be restricted. If it is not restricted, it should be achievable using `dig -t AXFR DOMAIN_NAME @DNS_SERVER`. The `-t AXFR` indicates that we are requesting a zone transfer, while `@` precedes the `DNS_SERVER` that we want to query regarding the records related to the specified `DOMAIN_NAME`.

* * * 

## SMB

We can check shared folders using `net share`

* * * 

## SNMP

Simple Network Management Protocol (SNMP) was designed to help collect information about different devices on the network. It lets you know about various network events, from a server with a faulty disk to a printer out of ink. Consequently, SNMP can hold a trove of information for the attacker. One simple tool to query servers related to SNMP is `snmpcheck`. the syntax is quite simple: `/opt/snmpcheck/snmpcheck.rb MACHINE_IP -c COMMUNITY_STRING`.

* * * 

## Conclusion

| Linux Command | Description |
|---------------|-------------|
| `hostname` | shows the system’s hostname |
| `who` | shows who is logged in |
| `whoami` | shows the effective username |
| `w` | shows who is logged in and what they are doing |
| `last` | shows a listing of the last logged-in users |
| `ip address show` | shows the network interfaces and addresses |
| `arp` | shows the ARP cache |
| `netstat` | prints network connections |
| `ps` | shows a snapshot of the current processes |

| Windows Command | Description |
|-----------------|-------------|
| `systeminfo` | shows OS configuration information, including service pack levels |
| `whoami` | shows the user name and group information along with the respective security identifiers |
| `netstat` | shows protocol statistics and current TCP/IP network connections |
| `net user` | shows the user accounts on the computer |
| `net localgroup` | shows the local groups on the computer |
| `arp` | shows the IP-to-Physical address translation tables |


* * * 