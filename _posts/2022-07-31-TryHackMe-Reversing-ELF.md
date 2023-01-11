---

title: Walkthrough - Reversing ELF
published: true
---

Reverse Engineering, ELF, CTF, Challenge, Radare2. Room for beginner Reverse Engineering CTF players

[https://tryhackme.com/room/reverselfiles](https://tryhackme.com/room/reverselfiles)

* * *

## Task 1 Crackme1

Let's start with a basic warmup, can you run the binary?

**What is the flag?**

- ``chmod +x crackme1`` lets us run the binary and get the flag.

* * * 

## Task 2 Crackme2

Find the super-secret password! and use it to obtain the flag

**What is the super secret password?**

- ``hexeditor crackme2`` - we can see the password

**What is the flag?**

- flag{if_i_submit_this_flag_then_i_will_get_points}

* * * 

## Task 3 Crackme3

Use basic reverse engineering skills to obtain the flag

**What is the flag?**

- ``strings crackme3`` we see a base64 enctpyed password

* * * 

## Task 4 Crackme4

Analyze and find the password for the binary?

**What is the password?**

- hint ``This time the string is hidden and we used strcmp``

- ``objdump -dj .text crackme4`` returns nothing
- ``readelf -a crackme4`` returns nothing
- ``gbd ./crackme4`` returns nothing 
- ``strace ./crackme4 password`` nothing
- ``ltrace ./crackme4 password`` bingo! - ``my_m0r3_secur3_pwd``

* * * 

## Task 5 Crackme5

What will be the input of the file to get output ``Good game``?

**What is the input?**

- ``ltrace ./crackme5`` gives us the needed input.

* * * 

## Task 6 Crackme6

Analyze the binary for the easy password - we have to use Radare2 for this

What is the password ?

- ``rabin2 -I crackme6`` - can be used to pull information out of a binary like strings, compile time, and other useful information
- ``rabin2 -z crackme6`` - gives us a hint ``2   0x00000810 0x00400810 47  48   .rodata ascii Usage : %s password\nGood luck, read the source\n``
- ``rabin2 -zz crackme6`` - lists all of the strings in the binary not just the strings in the data section like ``-z``

We can now load our binary into radare2 and analyze the binary. To do this we can ``r2 crackme6`` and run the command ``aa`` which is the basic analysis command for radare2. We also have ``aaa`` and ``aaaa`` each analyzed more information than the previous. 

Now that our binary is analyzed we can jump to a section where there is code. the ``s`` command iis used to seek a spot in the memory. running ``s main`` we can notice the cursor changed. Now we are at the main function. We can switch to visual mode now with ``v`` which shows us radare2's hex editor. Chaning the view to ``p`` we can see disassembly view and the assembly code of the program. I belive what we are going to need to look for is the ``sym.compare_pwd``

I was unable to find anything using rabin. Kind of a noob. But opening the file in Ghidra and browsing around I managed to find the password I was looking for.

![](/assets/reverselfiles01.png)

* * * 

## Task 7 Crackme7

Analyze the binary to get the flag

What is the flag ?

Changing the local 14 and saving the program we can get the key by accessing crackme7 again and inputting the number we modified.

![](/assets/reverselfiles02.png)

* * * 

##  Task 8 Crackme8

Analyze the binary and obtain the flag

**What is the flag ?**

Using the same steps as the last and converting the main param 1 IVar2 to binary we can use that to access the last flag.

![](/assets/reverselfiles03.png)

* * * 