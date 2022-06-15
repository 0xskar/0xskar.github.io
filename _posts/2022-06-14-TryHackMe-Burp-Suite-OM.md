---
title: TryHackMe - Burp Suite - Other Modules
published: true
---

[https://tryhackme.com/room/burpsuiteom](https://tryhackme.com/room/burpsuiteom)

Take a dive into some of Burp Suite's lesser known modules

* * *

# [](#header-3)Task 1 - Introduction - Room Outline 

Specifically, we will be looking at the Decoder, Comparer and Sequencer tools. These allow us to: work with encoded text; compare sets of text; and analyse the randomness of captured tokens, respectively. Being able to perform these relatively straightforward tasks directly within Burp Suite can save a lot of time, so it is well worth the time spent learning to use these modules efficiently.

* * * 

# [](#header-3)Task 2 - Decoder - Overview

Just familiarising with the Decoder Interface.

* * * 

# [](#header-3)Task 3 - Decoding/Encoding

**Base64 encode the phrase: ``Let's Start Simple``.**

**What is the base64 encoded version of this text?** TGV0J3MgU3RhcnQgU2ltcGxl

**URL Decode this data:** ``%4e%65%78%74%3a%20%44%65%63%6f%64%69%6e%67``.

**What is the plaintext returned?** Next: Decoding

**Use Smart Decode to decode this data:** ``&#x25;&#x33;&#x34;&#x25;&#x33;&#x37;``.

**What is the decoded text?** 47

**Encode this phrase:** ``Encoding Challenge``.

**Start with base64 encoding. Take the output of this and convert it into ASCII Hex. Finally, encode the hex string into octal.**

**What is the final string?** 24034214a720270024142d541357471232250253552c1162d1206c

* * * 

# [](#header-3)Task 4 - Decoder - Hashing


**Using Decoder, what is the SHA-256 hashsum of the phrase: Let's get Hashing!?
Convert this into an ASCII Hex string for the answer to this question.** 6b72350e719a8ef5af560830164b13596cb582757437e21d1879502072238abe

**Generate an MD4 hashsum of the phrase: Insecure Algorithms. Encode this as base64 (not ASCII Hex) before submitting.** TcV4QGZZN7y7lwYFRMMoeA==

Let's look at an in-context example:

First, download the file attached to this task.

Note: This file can also be downloaded from the deployed VM with  wget http://10.10.117.71:9999/AlteredKeys.zip -- you may find this useful if you are using the AttackBox.

Now read the problem specification below:

"Some joker has messed with my SSH key! There are four keys in the directory, and I have no idea which is the real one. The MD5 hashsum for my key is 3166226048d6ad776370dc105d40d9f8 -- could you find it for me?"

Submit the correct key name as your answer.







