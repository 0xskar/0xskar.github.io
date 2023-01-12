---
title: Resources
date: 2023-01-10 23:49:00 -0500
categories: [Resources]
tags: [Resources]
pin: true 
---

A collection of links i've found useful. Tools/Guides/Websites.

## Useful Tools

- [GTFO Bins](https://gtfobins.github.io/) - Linux Binaries
- [LOLBAS](https://lolbas-project.github.io/) - GTFO Bins for Windows
- [RevShells](https://www.revshells.com/) - Reverse Shell Generator 
- [PenTest.WS]()
- [CyberChef Encoder/Decoder](https://gchq.github.io/CyberChef/)
- [List of File Signatures](https://en.wikipedia.org/wiki/List_of_file_signatures)
- [Bash Scripting Cheatsheet](https://devhints.io/bash)
- [Google Hacking Database](https://www.exploit-db.com/google-hacking-database) - Useful google dorking
- [XSS Cheatsheet](https://3os.org/penetration-testing/cheatsheets/xss-cheatsheet/#img-onerror-and-javascript-alert-encode)- a series of XSS attacks that can be used to bypass certain XSS defensive filters

## Useful Guides

- [Hacktricks.xyz Pentesting Methodology](https://book.hacktricks.xyz/welcome/readme)
- [Buffer Overflow Exploit Guide](https://github.com/Tib3rius/Pentest-Cheatsheets/blob/master/exploits/buffer-overflows.rst)

## Misc Tools

- [FileSEC.io](https://filesec.io/) - latest file extensions being used by attackers 
- [LOTS Project](https://lots-project.com/) - Attackers are using popular legitimate domains when conducting phishing, C&C, exfiltration and downloading tools to evade detection. The list of websites below allow attackers to use their domain or subdomain.
- [Responder](https://www.kali.org/tools/responder/) - Steal NTLM hashes. LLMNR NBT-NS, MDNS poisoner

## Passwords/Hashes

- [hashcat wiki](https://hashcat.net/wiki/doku.php?id=example_hashes) - hash types and examples
- [https://cirt.net/passwords](https://cirt.net/passwords) - default passwords
- [https://default-password.info/](https://default-password.info/) - default passwords
- [https://datarecovery.com/rd/default-passwords/](https://datarecovery.com/rd/default-passwords/) - default passwords

## Privilege Escalation

- [Hacktricks Windows Local Privilege Escalation Checklist](https://book.hacktricks.xyz/windows-hardening/checklist-windows-privilege-escalation)
- [Hacktricks Linux Privilege Escalation Checklist](https://book.hacktricks.xyz/linux-hardening/linux-privilege-escalation-checklist)
- [Payload All the THings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)
- [Useful Linux PrivEsc Commands](https://0xskar.github.io/TryHackMe-Linux-PrivEsc)

## Command Line

- [SS64](https://ss64.com/) - Command line refrences for all systems
- [ExplainShell](https://www.explainshell.com/)

## SQL Tools

- [SQLMap](https://github.com/sqlmapproject/sqlmap) - Preinstalled on Kali. Automatic SQL injection and database takeover tool.
- 

## DNS Queries

- ``nslookup --type=CNAME website`` also ``--type=A``, ``--type=MX``, ``--type=TXT``

## HTTP Scanners

- ``joomscan`` - scans Joomla CMS'
- ``shodan`` - search for various types of servers connected to the internet using a variety of filters.
- ``theHarvester`` - a tool for gathering subdomain names, e-mail addresses, virtual hosts, open ports/ banners, and employee names from different public sources
- [joomblah](https://github.com/stefanlucas/Exploit-Joomla/blob/master/README.md) -  SQL Injection for Joomla - it will dump the users and session tables
- ``nikto``
- ``feroxbuster`` -  a tool designed to perform Forced Browsing. Forced browsing is an attack where the aim is to enumerate and access resources that are not referenced by the web application, but are still accessible by an attacker.


## Active Directory

- [crackmapexec](https://mpgn.gitbook.io/crackmapexec/) - Preinstalled on Kali - Post-exploitation tool that helps automate assessing security of large Active Directory networks and find misconfigurations.

## Powershell

- [Full list of operators](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/where-object?view=powershell-7.2&viewFallbackFrom=powershell-6)
- [Full List of Approved Verbs](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/approved-verbs-for-windows-powershell-commands?view=powershell-7)
- [Learn Powershell in Y Minutes](https://learnxinyminutes.com/docs/powershell/)

## OSINT

- [ViewDNS.info](https://viewdns.info) - DNS History

## SOC Stuff/Malware Analysis

- [VirusTotal](https://www.virustotal.com) scan checksums of files to determine if maliciouis.
- [Metadefender Cloud - OPSWAT](https://metadefender.opswat.com/?lang=en) 
- [https://dmarcian.com/](https://dmarcian.com/) Check DMARC and SPF records
- [The DFIR Report](https://thedfirreport.com/)
- [FireEye Threat Research Blogs](https://www.fireeye.com/blog/threat-research.html)
- [AnyRun](https://any.run/) - Interactive Malware Hunting Service

## Memory Analysis

- [Volatilty Cheatsheet](https://blog.onfvp.com/post/volatility-cheatsheet/)

## Practice/Testing

- [Testing XSS Skills](https://www.acunetix.com/blog/web-security-zone/test-xss-skills-vulnerable-sites/)

## Evasion Techniques

- [Fast Flux](https://unit42.paloaltonetworks.com/fast-flux-101/) - How Cybercriminals Improve the Resilience of Their Infrastructure to Evade Detection and Law Enforcement Takedowns

* * *