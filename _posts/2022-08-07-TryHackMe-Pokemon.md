---
title: Walkthrough - Gotta Catch'em All!
published: true
---

Security, Pokemon, Encoding, Cipher. A room based on the original Pokemon series. Can you obtain all the Pokemon in this room?

[https://tryhackme.com/room/pokemon](https://tryhackme.com/room/pokemon)

* * *

## Notes

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 58:14:75:69:1e:a9:59:5f:b2:3a:69:1c:6c:78:5c:27 (RSA)
|   256 23:f5:fb:e7:57:c2:a5:3e:c2:26:29:0e:74:db:37:c2 (ECDSA)
|_  256 f1:9b:b5:8a:b9:29:aa:b6:aa:a2:52:4a:6e:65:95:c5 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Can You Find Them All?
|_http-server-header: Apache/2.4.18 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Adtran 424RG FTTH gateway (92%), Linux 2.6.32 (92%), Linux 2.6.39 - 3.2 (92%), Linux 3.1 - 3.2 (92%), Linux 3.11 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

- a few things we find in the source code

```
<script type="text/javascript">
    	const randomPokemon = [
    		'Bulbasaur', 'Charmander', 'Squirtle',
    		'Snorlax',
    		'Zapdos',
    		'Mew',
    		'Charizard',
    		'Grimer',
    		'Metapod',
    		'Magikarp'
    	];
    	const original = randomPokemon.sort((pokemonName) => {
    		const [aLast] = pokemonName.split(', ');
    	});

    	console.log(original);
    </script>
```

```
        <pokemon>:<hack_the_pokemon>
        	<!--(Check console for extra surprise!)-->
```

- we are able to ssh in with these credentials

* * * 

## Find the Grass-Type Pokemon

- ``ls -lAh *`` we find a file in ``/Desktop`` and a folder in ``/Videos``, ``/Gotta``
- The ``/Desktop`` directory has a zipfile lets ``scp pokemon@10.10.154.81:/home/pokemon/Desktop/P0kEmOn.zip .`` and check it out. ``7z x P0kEmOn.zip``. It contains an ASCII with the grass pokemon.

* * * 

## Find the Water-Type Pokemon

- ``find / -type f -name *.txt -user pokemon 2>/dev/null``
- ``cat /var/www/html/water-type.txt``
- decode the caesar cipher

* * * 

## Find the Fire-Type Pokemon

- Swap to ash. Travel to ``~/Videos/Gotta/Catch/Them/ALL!`` to find ash's credentials and ``su ash``
- ``sudo -l``

```
User ash may run the following commands on root:
    (ALL : ALL) ALL
```

- ``sudo bash``
- ``find / -name '*fire*' -type f 2>/dev/null``
- ``/etc/why_am_i_here?/fire-type.txt``
- decode base64

* * * 

## Who is Root's Favorite Pokemon?

- ``cat roots-pokemon.txt``

* * * 


╔══════════╣ PATH
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#writable-path-abuses                                 
/home/pokemon/bin:/home/pokemon/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
New path exported: /home/pokemon/bin:/home/pokemon/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
