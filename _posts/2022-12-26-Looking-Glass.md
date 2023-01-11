---
title: Looking Glass
date: 2022-12-26 18:32:00 -0500
categories: [Walkthrough, Tryhackme, CTF]
tags: [wonderland, ctf, alice, ssh]
---

<https://tryhackme.com/room/lookingglass> Step through the looking glass. A sequel to the Wonderland challenge room. 

<img src="/assets/Aliceroom2.jpg" alt="lOOKING gLASS">

A sequel to the <a href="https://0xskar.github.io/tryhackme%20walkthrough/2022/09/19/TryHackMe-Wonderland.html">Wonderland</a> room.


Nmap scanning `nmap -p- lookingglass.thm -vvv`....Holy open ports batmanm theres a lot of them. Never had to deal with this before.... It looks like the server has some sort of Firewall. Checking the nmap manual we have some flags just for this scenario...

```
             -f; --mtu <val>: fragment packets (optionally w/given MTU)
             -D <decoy1,decoy2[,ME],...>: Cloak a scan with decoys
             -S <IP_Address>: Spoof source address
             -e <iface>: Use specified interface
             -g/--source-port <portnum>: Use given port number
             --proxies <url1,[url2],...>: Relay connections through HTTP/SOCKS4 proxies
             --data <hex string>: Append a custom payload to sent packets
             --data-string <string>: Append a custom ASCII string to sent packets
             --data-length <num>: Append random data to sent packets
             --ip-options <options>: Send packets with specified ip options
             --ttl <val>: Set IP time-to-live field
             --spoof-mac <mac address/prefix/vendor name>: Spoof your MAC address
             --badsum: Send packets with a bogus TCP/UDP/SCTP checksum
```

Using `-f`, `--mtu 8`, `--badsum` no matter the flag still getting open ports however. Scanning top 100 ports `-F` shows us which of the top 100 are closed/open however this doesnt really help for trying to scan the full machine hmmm....

- `sudo nmap -Pn -sS -p- lookingglass.thm -vvv -r`

the `-r` lets us scan flags sequentually so its not jumping all over the place. performing this scan shows us port 22 is open then 9000-13999 are open dropbear sshd servers. Trying to connect to a port will show higher or lower where higher you go lower and for lower you go higher. Keep trying this until you find the open port

- `ssh lookingglass.thm -p 13803`

And we find a note...

```
You've found the real service.
Solve the challenge to get access to the box
Jabberwocky
'Mdes mgplmmz, cvs alv lsmtsn aowil
Fqs ncix hrd rxtbmi bp bwl arul;
Elw bpmtc pgzt alv uvvordcet,
Egf bwl qffl vaewz ovxztiql.

'Fvphve ewl Jbfugzlvgb, ff woy!
Ioe kepu bwhx sbai, tst jlbal vppa grmjl!
Bplhrf xag Rjinlu imro, pud tlnp
Bwl jintmofh Iaohxtachxta!'

Oi tzdr hjw oqzehp jpvvd tc oaoh:
Eqvv amdx ale xpuxpqx hwt oi jhbkhe--
Hv rfwmgl wl fp moi Tfbaun xkgm,
Puh jmvsd lloimi bp bwvyxaa.

Eno pz io yyhqho xyhbkhe wl sushf,
Bwl Nruiirhdjk, xmmj mnlw fy mpaxt,
Jani pjqumpzgn xhcdbgi xag bjskvr dsoo,
Pud cykdttk ej ba gaxt!

Vnf, xpq! Wcl, xnh! Hrd ewyovka cvs alihbkh
Ewl vpvict qseux dine huidoxt-achgb!
Al peqi pt eitf, ick azmo mtd wlae
Lx ymca krebqpsxug cevm.

'Ick lrla xhzj zlbmg vpt Qesulvwzrr?
Cpqx vw bf eifz, qy mthmjwa dwn!
V jitinofh kaz! Gtntdvl! Ttspaj!'
Wl ciskvttk me apw jzn.

'Awbw utqasmx, tuh tst zljxaa bdcij
Wph gjgl aoh zkuqsi zg ale hpie;
Bpe oqbzc nxyi tst iosszqdtz,
Eew ale xdte semja dbxxkhfe.
Jdbr tivtmi pw sxderpIoeKeudmgdstd
Enter Secret:
```

Using <a href="https://www.boxentriq.com/code-breaking/vigenere-cipher">Boxenteiq's</a> cipher auto solver we can get the key to the vinegre cipher. then inputing it into <a href="https://www.dcode.fr/vigenere-cipher">dcode.fr</a> we can unlock and get the whole story.

Entering the secret gives us some credentials

- `jabberwock:RamblingFilledOutsideCarrying`

these will get us into the port 22 ssh. and then we can collect the user flag, which is mirrored as well.

`sudo -l` lets us know we can reboot the pc as admin. bahaha I knew this would reboot and did it anyways, oh well we have the ssh creds. EXCEPT WHEN YOU REBOOT IS COMPLETELY RESETS THE PASSWORD AS WELL FUCK. Time to go through a thousand ports to find a way in again. 

a new credential, it definitly spawns a new pass every restart so dont do that anymore.

- `jabberwock:BillowsUpwardsKindlyLeisurely`

we have a few different users here

jabberwork, tweedledum, tweedledee, humptydumpty, and alice

checking crontab, we have tweedledum running a bash script on reboot: @reboot tweedledum bash /home/jabberwock/twasBrillig.sh

To me - it doesnt look like there is anywhere really to go from here other than to tweedledum, which means rebooting the machine again. Lets edit the script to send a reverse shell.

# tweedledum

- `cat humptydympty.txt

```
dcfff5eb40423f055a4cd0a8d7ed39ff6cb9816868f5766b4088b9e9906961b9
7692c3ad3540bb803c020b3aee66cd8887123234ea0c6e7143c0add73ff431ed
28391d3bc64ec15cbb090426b04aa6b7649c3cc85f11230bb0105e02d15e3624
b808e156d18d1cecdcc1456375f8cae994c36549a07c8c2315b473dd9d7f404f
fa51fd49abf67705d6a35d18218c115ff5633aec1f9ebfdc9d5d4956416f57f6
b9776d7ddf459c9ad5b0e1d6ac61e27befb5e99fd62446677600d7cacef544d0
5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8
7468652070617373776f7264206973207a797877767574737271706f6e6d6c6b
```

these are sha-256, except for the last one which is the one we need. its hexadecimal, we can feed it to cyberchef and get the password for humptydumpty:zyxwvutsrqponmlk

checking the file perms for alice in the home dir we notice some strange permissions and can actually traverse her directory. we're able to grab her private id_rsa and then connect with it from our attacker via SSH.

Running linpeas earliar we noticed we could read the sudoers.d and she can run /bin/bash as root as long as they are doing it from the host ssalg-gnikool. we can specify this host with the `-h` flag and gain root with `sudo -h ssalg-gnikool /bin/bash`

Fun machine! took me a few hours!