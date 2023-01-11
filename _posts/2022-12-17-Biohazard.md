---
title: Biohazard
date: 2022-12-27 18:32:00 -0500
categories: [Walkthrough, Tryhackme, CTF]
tags: [cipher, base, stego, root, linux]
---

<https://tryhackme.com/room/biohazard> A CTF room based on the old-time survival horror game, Resident Evil. Can you survive until the end?

# Notes

perform nmap scans.

checking the port 80 we fnid the team name STARS alpha team. checking the source on /mansionmain/ hints to /diningroom/ and checking the dining room we find base64 comment. `SG93IGFib3V0IHRoZSAvdGVhUm9vbS8=`

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Biohazard]
└─$ echo "SG93IGFib3V0IHRoZSAvdGVhUm9vbS8=" | base64 -d
How about the /teaRoom/   
```

following this clue we can find our lock pick flag, then check out the artRoom amd get the sitemap.

/diningRoom/<br>
/teaRoom/<br>
/artRoom/<br>
/barRoom/<br>
/diningRoom2F/<br>
/tigerStatusRoom/<br>
/galleryRoom/<br>
/studyRoom/<br>
/armorRoom/<br>
/attic/<br>

Checking the emblem in the /diningRoom/ gives us the emblem flag

checking the barRoom and giving the lockpick we can access a music note which is base32 decoded to the music sheet flag. we can enter the flag previius room and get the gold emblem, which can be placed in the dining room.

inserting the gold emblem into the dining room emblem slot gives us a vigenere with the key rebecca that translates to `there is a shield key inside the dining room. The html page is called the_great_shield_key`. accessing the_great_sheild_key.html gives us the shield key.

/tigerStatusRoom/

```
crest 1:
S0pXRkVVS0pKQkxIVVdTWUpFM0VTUlk9
Hint 1: Crest 1 has been encoded twice
Hint 2: Crest 1 contanis 14 letters
Note: You need to collect all 4 crests, combine and decode to reavel another path
The combination should be crest 1 + crest 2 + crest 3 + crest 4. Also, the combination is a type of encoded base and you need to decode it
```

crest1 using cyberchef is decoded from base32 from base64 to: RlRQIHVzZXI6IG



/galleryRoom/ has a note

```
crest 2:
GVFWK5KHK5WTGTCILE4DKY3DNN4GQQRTM5AVCTKE
Hint 1: Crest 2 has been encoded twice
Hint 2: Crest 2 contanis 18 letters
Note: You need to collect all 4 crests, combine and decode to reavel another path
The combination should be crest 1 + crest 2 + crest 3 + crest 4. Also, the combination is a type of encoded base and you need to decode it
```

crest2 using cyberchef is decoded from base58 from base32 to: h1bnRlciwgRlRQIHBh



/armorRoom/

```
crest 3:
MDAxMTAxMTAgMDAxMTAwMTEgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAwMTEgMDAxMDAwMDAgMDAxMTAxMDAgMDExMDAxMDAgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAxMTAgMDAxMDAwMDAgMDAxMTAxMDAgMDAxMTEwMDEgMDAxMDAwMDAgMDAxMTAxMDAgMDAxMTEwMDAgMDAxMDAwMDAgMDAxMTAxMTAgMDExMDAwMTEgMDAxMDAwMDAgMDAxMTAxMTEgMDAxMTAxMTAgMDAxMDAwMDAgMDAxMTAxMTAgMDAxMTAxMDAgMDAxMDAwMDAgMDAxMTAxMDEgMDAxMTAxMTAgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTEwMDEgMDAxMDAwMDAgMDAxMTAxMTAgMDExMDAwMDEgMDAxMDAwMDAgMDAxMTAxMDEgMDAxMTEwMDEgMDAxMDAwMDAgMDAxMTAxMDEgMDAxMTAxMTEgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAxMDEgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAwMDAgMDAxMDAwMDAgMDAxMTAxMDEgMDAxMTEwMDAgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAwMTAgMDAxMDAwMDAgMDAxMTAxMTAgMDAxMTEwMDA=
Hint 1: Crest 3 has been encoded three times
Hint 2: Crest 3 contanis 19 letters
Note: You need to collect all 4 crests, combine and decode to reavel another path
The combination should be crest 1 + crest 2 + crest 3 + crest 4. Also, the combination is a type of encoded base and you need to decode it
```

crest3 using cyberchef is decoded to: c3M6IHlvdV9jYW50X2h



checking /diningRoom2F/ source code gives us a rot13 cipher which points us to /sapphire.html where we pick up the blue jewel.

/attic/ we insert the shield key and get another note

```
crest 4:
gSUERauVpvKzRpyPpuYz66JDmRTbJubaoArM6CAQsnVwte6zF9J4GGYyun3k5qM9ma4s
Hint 1: Crest 2 has been encoded twice
Hint 2: Crest 2 contanis 17 characters
Note: You need to collect all 4 crests, combine and decode to reavel another path
The combination should be crest 1 + crest 2 + crest 3 + crest 4. Also, the combination is a type of encoded base and you need to decode it
```

crest4 using cyberchef is decoded to: pZGVfZm9yZXZlcg==

adding all crest together we get: RlRQIHVzZXI6IGh1bnRlciwgRlRQIHBhc3M6IHlvdV9jYW50X2hpZGVfZm9yZXZlcg==

we get some credentials: FTP user: hunter, FTP pass: you_cant_hide_forever

access the ftp server and mget the files, lets check them out.

there is a helmet_key.txt.gpg with probably the helmet key but we need the private key in order to decrypt. also another room in important.txt `/hidden_closet/` which we need the key to unlock.

we have a hint for the three pics. hide, comment and walk away.

`stegseek 001-key.jpg` we get another pass/hash? `cGxhbnQ0Ml9jYW` for our hide

`exiftool 002-key-jpg` we can see the comment `5fYmVfZGVzdHJveV9`

and for the final pic `unzip 003-key.jpg` which contains `3aXRoX3Zqb2x0`

together: cGxhbnQ0Ml9jYW5fYmVfZGVzdHJveV93aXRoX3Zqb2x0

thrown into cyberchef gives us: `plant42_can_be_destroy_with_vjolt`

`gpg helmet_key.txt.gpg` with the pass give us the helmet key.

- http://biohazard.thm/hiddenCloset8997e740cb7f5cece994381b9477ec38/

MO disk 1 - `wpbwbxr wpkzg pltwnhro, txrks_xfqsxrd_bvv_fy_rvmexa_ajk`

Wolf medal - `SSH password: T_virus_rules`

going back to the studyRoom and insert the helmet key, there we get `doom.tar.gz` which contains the user `umbrella_guest` these creds get us into the ssh.

* * * 

# machine pwn

- `cat .jailcell/chris.txt`

```
Jill: Chris, is that you?
Chris: Jill, you finally come. I was locked in the Jail cell for a while. It seem that weasker is behind all this.
Jil, What? Weasker? He is the traitor?
Chris: Yes, Jill. Unfortunately, he play us like a damn fiddle.
Jill: Let's get out of here first, I have contact brad for helicopter support.
Chris: Thanks Jill, here, take this MO Disk 2 with you. It look like the key to decipher something.
Jill: Alright, I will deal with him later.
Chris: see ya.

MO disk 2: albert 
```

also a note in /home/weasker

```
umbrella_guest@umbrella_corp:/home/weasker$ cat weasker_note.txt 
Weaker: Finally, you are here, Jill.
Jill: Weasker! stop it, You are destroying the  mankind.
Weasker: Destroying the mankind? How about creating a 'new' mankind. A world, only the strong can survive.
Jill: This is insane.
Weasker: Let me show you the ultimate lifeform, the Tyrant.

(Tyrant jump out and kill Weasker instantly)
(Jill able to stun the tyrant will a few powerful magnum round)

Alarm: Warning! warning! Self-detruct sequence has been activated. All personal, please evacuate immediately. (Repeat)
Jill: Poor bastard
```

the MO disk 1 is a vigenere cipher which translates to `weasker login password, stars_members_are_my_guinea_pig`

`su weasker` and check `sudo -l`

```
User weasker may run the following commands on umbrella_corp:
    (ALL : ALL) ALL
```

easy sudo bash for root. where we can pickup the root flag
