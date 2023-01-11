---
title: Walkthrough - Anthem
published: true
---

Exploit a Windows machine in this beginner level challenge.

[https://tryhackme.com/room/anthem](https://tryhackme.com/room/anthem)

* * *

## Notes

This task involves you, paying attention to details and finding the 'keys to the castle'.

This room is designed for beginners, however, everyone is welcomed to try it out!

Enjoy the Anthem.

In this room, you don't need to brute force any login page. Just your preferred browser and Remote Desktop.

Please give the box up to 5 minutes to boot and configure.

* * * 

## Let's run nmap and check what ports are open.

- ``sudo nmap -Pn -sS -p- -T4 10.10.64.102 -vvv``

* * * 

## What port is for the web server?

- ``sudo nmap -Pn -sS -p- -T4 10.10.64.102 -vvv``

* * * 

## What port is for remote desktop service?

- ``sudo nmap -Pn -sS -p- -T4 10.10.64.102 -vvv``

* * * 

## What is a possible password in one of the pages web crawlers check for?

- check robots.txt

```html
UmbracoIsTheBest!

# Use for all search robots
User-agent: *

# Define the directories not to crawl
Disallow: /bin/
Disallow: /config/
Disallow: /umbraco/
Disallow: /umbraco_client/
```

* * * 

## What CMS is the website using?

- Ubramco

* * * 

## What is the domain of the website?

- anthem.com

* * * 

## What's the name of the Administrator

- Solomon Grundy - Clue in the poem - http://10.10.143.193/archive/a-cheers-to-our-it-department/

* * * 

## Can we find find the email address of the administrator?

- sg@anthem.com

* * * 

## Let's figure out the username and password to log in to the box.(The box is not on a domain)

- we can login to /umbraco/ with the credentials that we found sg@anthem.com:UmbracoIsTheBest!

* * * 

## Gain initial access to the machine, what is the contents of user.txt?

- we can login to the rdp with sg:UmbracoIsTheBest!


* * * 

## Can we spot the admin password?

Hidden restore directory in ``c:\``. There is a password located in the restore file that is viewable after changing permissions tot he file.

* * * 

## Escalate your privileges to root, what is the contents of root.txt?

-login with admin creds

* * * 
