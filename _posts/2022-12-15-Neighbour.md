---

title: Neighbour 
published: true
category: Lessons
system: Web
walkthrough-type: thm
source: https://tryhackme.com/room/neighbour
Description: Check out our new cloud service, Authentication Anywhere. Can you find other user's secrets?
tags: tdor, web, ctf, authentication
---

* * * 

# Find the flag on your neighbor's logged in page!

Start with nmap scan

Ports open 22, and 80

checking source code `<!-- use guest:guest credentials until registration is fixed. "admin" user account is off limits!!!!! -->`

From there the flag is a pretty easy insecure direct object refrence away.