---
title: Practical - Gatekeeper
published: true
---

[https://tryhackme.com/room/gatekeeper](https://tryhackme.com/room/gatekeeper)

Can you get past the gate and through the fire?

![](/assets/balrog.jpg)

* * *

## [](#header-2)Defeat the Gatekeeper and pass through the fire. 

Defeat the Gatekeeper to break the chains.  But beware, fire awaits on the other side.

### [](###header-3)NMap Scans

| PORT | STATE | SERVICE | REASON |
|------|-------|---------|--------|
| 135/tcp | open | msrpc | syn-ack ttl 125 |
| 139/tcp | open | netbios-ssn | syn-ack ttl 125 |
| 445/tcp | open | microsoft-ds | syn-ack ttl 125 |
| 3389/tcp | open | ms-wbt-server | syn-ack ttl 125 |
| 31337/tcp | open | Elite | syn-ack ttl 125 |
| 49152/tcp | open | unknown | syn-ack ttl 125 |
| 49153/tcp | open | unknown | syn-ack ttl 125 |
| 49154/tcp | open | unknown | syn-ack ttl 125 |
| 49155/tcp | open | unknown | syn-ack ttl 125 |
| 49161/tcp | open | unknown | syn-ack ttl 125 |

### [](###header-3)Port 135 - MSRPC

- Microsoft Windows RPC
- ``rpcdump.py 135 10.10.119.214``

##### [](###header-4)msf6 auxiliary(scanner/dcerpc/endpoint_mapper) > run
- 10.10.119.214:135     - 30b044a5-a225-43f0-b3a4-e060df91f9c1 v1.0 PIPE (\PIPE\atsvc) \\GATEKEEPER 
- Task Scheduler, used to remote execute commands
- 10.10.119.214:135     - 30b044a5-a225-43f0-b3a4-e060df91f9c1 v1.0 PIPE (\PIPE\srvsvc) \\GATEKEEPER 




##### [](###header-5)``msf6 auxiliary(scanner/dcerpc/tcp_dcerpc_auditor) > run``
- 10.10.119.214 - UUID 99fcfec4-5260-101b-bbcb-00aa0021347a 0.0 OPEN VIA 135 ACCESS GRANTED 00000000000000000000000000000000000000000000000005000000


### [](###header-3)Port 139 - NetBios

- Microsoft Windows netbois-ssn

### [](###header-3)Port 445 - SMB

- Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
- Downloaded Files - but doesnt seem to be anything of worth  maybeupload something here later

### [](###header-3)Port 3389 - RDP - Remote Desktop

```shell
| rdp-ntlm-info: 
|   Target_Name: GATEKEEPER
|   NetBIOS_Domain_Name: GATEKEEPER
|   NetBIOS_Computer_Name: GATEKEEPER
|   DNS_Domain_Name: gatekeeper
|   DNS_Computer_Name: gatekeeper
|   Product_Version: 6.1.7601
|_  System_Time: 2022-07-02T11:53:05+00:00
| ssl-cert: Subject: commonName=gatekeeper
| Issuer: commonName=gatekeeper
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2022-07-01T11:00:07
| Not valid after:  2022-12-31T11:00:07
| MD5:   cbe7 25b5 c626 4c1b 4e04 4262 87c3 f124
| SHA-1: 2289 4297 692e e7c8 ec29 c106 d989 444a 8456 94c2
| -----BEGIN CERTIFICATE-----
| MIIC2DCCAcCgAwIBAgIQcciRmCUj94ROLW7etCoivTANBgkqhkiG9w0BAQUFADAV
| MRMwEQYDVQQDEwpnYXRla2VlcGVyMB4XDTIyMDcwMTExMDAwN1oXDTIyMTIzMTEx
| MDAwN1owFTETMBEGA1UEAxMKZ2F0ZWtlZXBlcjCCASIwDQYJKoZIhvcNAQEBBQAD
| ggEPADCCAQoCggEBAIzfBHtMDD+WWq57O3k5jQ4RImtU/E1I158SzfY4Katmh0Ar
| ulx+QRQCulZG2mdO4SEl47lohzowdTRJD7PiEE5kkL9W4agaEHV9+2QsX44qhVfm
| 7Qn6Yyvff2H4i0velF3WEWD5vp0116EEaKJhY4qqmhy3D53YXn7CKmarS5zts5p8
| KZhr4eRSTv2fTXnb/ywMEZL1ZjOb/CQtL/MsTdOVn6+UDCMLX0pfJUgyKuajTw+7
| L6G2gFmvMrL6dWzK6C3QAhJ0+F9MaVPGZdG/xt/Zt3sXwigbvfyEQTJqnoadS1Oo
| IXUgYBmLhxHHg/IXxk9gpNwJsUD5uPGD4DnqphkCAwEAAaMkMCIwEwYDVR0lBAww
| CgYIKwYBBQUHAwEwCwYDVR0PBAQDAgQwMA0GCSqGSIb3DQEBBQUAA4IBAQANJWz9
| aKt35PpZerpN+rvxQjxnWfdb0iyhWKS/JUQmciIf4szcxgI+WyeS/dUMx4Kpc+8E
| bKGcwoWESyXnlYM7YiBevqby7T9hXltS1mvWIbanTQdMh3qCg40oIZu5KNbtY2zd
| SzdnC06Zrx317txUCxAjaEnY8nE/f7S3OqU8XR0IZGO+DheGrS7ZcykfCP4kEaGr
| yzkfQMKnnVb0zAf9qoESspnMcbOiQ6AMOEcx2lwG9DAQPefF1SAr86T4oTbJ5xCK
| +wPKrjbMxAJSqWwmGB1FmnU9giqKMHrj9tMPKhS38vqtw6HvyLf83bv70cGhtkPX
| +tJAhkraDaMH53Iv
|_-----END CERTIFICATE-----
```

### [](###header-3)Port 31337 - Elite

- Seems to be webserver ot something?

### [](###header-3)Port 49153, 49153, 49154, 49155, 49161

- Microsoft Remote Procedure Call RPC?

##### [](#header-5)Answer the questions below



**Locate and find the User Flag.**



**Locate and find the Root Flag**



* * *



