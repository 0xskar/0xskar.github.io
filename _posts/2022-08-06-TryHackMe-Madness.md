---
title: Walkthrough - Madness
published: true
---

Security, Steganography, Web, Challenge. Will you be consumed by madness?

![0xskar](/assets/madness01.png)

[https://tryhackme.com/room/madness](https://tryhackme.com/room/madness)

* * *

## Notes

2 ports, 22 ssh and 80 http server.

Inspect source and find comment

```
<img src="thm.jpg" class="floating_element"/>
<!-- They will never find me-->
        <span class="floating_element">
```

- ``wget http://10.10.205.63/thm.jpg``
- ``hexeditor`` shows this jpg is set as a png. lets try to edit the file signature to be able to read it.
- using JFIF file signature header (``FF D8 FF E0 00 10 4A 46 49 46 00 01``) with ``hexeditor`` we can now read the file in ``ristretto``

![0xskar](/assets/madness02.png)

- found hidden directory ``/th1s_1s_h1dd3n``
- inspecting the source

```
<div class="main">
<h2>Welcome! I have been expecting you!</h2>
<p>To obtain my identity you need to guess my secret! </p>
<!-- It's between 0-99 but I don't think anyone will look here-->

<p>Secret Entered: </p>

<p>That is wrong! Get outta here!</p>
</div>
```

We need a secret to get access, so lets create a simple python script to make a list of 0-99. 

- ``numbers.py``

```python
for number in range(-1, 100):
	print(number)
```

- ``python3 numbers.py > numbers.txt``
- then fuzz ``gobuster fuzz -u http://10.10.205.63/th1s_1s_h1dd3n/?secret=FUZZ -w numbers.txt --no-error``
- one has a longer length ``http://10.10.205.63/th1s_1s_h1dd3n/?secret=73``
- where we get a new clue

```
<p>Secret Entered: 73</p>

<p>Urgh, you got it right! But I won't tell you who I am! y2RPJ4QaPF!B</p>
```

``hint - There's something ROTten about this guys name!``

Finally after hours of trying to decipher the above code, it turded out to be a password for the file hidden in the origional JPG that we found.

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Madness]
└─$ stegseek -sf thm-edited.jpg -wl secret.txt                               
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "y2RPJ4QaPF!B"
[i] Original filename: "hidden.txt".
[i] Extracting to "thm-edited.jpg.out".
```

- ``cat thm-edited.jpg.out``

```
Fine you found the password! 

Here's a username 

wbxre

I didn't say I would make it easy for you!
```

- and decrupting rot-10 we get the username ``joker``

- the header pic of the cheshire car smile contains another hidden text file..cheaters.

```
┌──(0xskar㉿cocokali)-[~/Documents/TryHackMe/Madness]
└─$ cat 5iW7kC8.jpg.out 
I didn't think you'd find me! Congratulations!

Here take my password

*axA&GF8dP
```

* * * 

## What is the user flag?

![0xskar](/assets/madness03.png)

* * * 

## What is the root flag?

Potential Escalation Vectors

- ``find / -perm -u=s -type f 2>/dev/null``
- ``searchsploit screen 4.5``
- ``cp /usr/share/exploitdb/exploits/linux/local/41154.sh .``
- wget it over and ``./41154.sh`` to get root

![0xskar](/assets/madness04.png)

* * * 

