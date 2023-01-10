---
layout: post
title: Walkthrough - Juicy Details
published: true
---

Blue Team, Security Operations, Logs, Web. A popular juice shop has been breached! Analyze the logs to see what had happened...

[https://tryhackme.com/room/juicydetails](https://tryhackme.com/room/juicydetails)

* * *

## Notes

We were hired as a SOC Analyst for one of the biggest Juice Shops in the world and an attacker has made their way into your network. 

Tasks are:

   - Figure out what techniques and tools the attacker used
   - What endpoints were vulnerable
   - What sensitive data was accessed and stolen from the environment

* * * 

##  Task 2 Reconnaissance

Analyze the provided log files.

Look carefully at:

  -  What tools the attacker used
  -  What endpoints the attacker tried to exploit
  -  What endpoints were vulnerable

**What tools did the attacker use? (Order by the occurrence in the log)**

- nmap, hydra, sqlmap, curl, feroxbuster

**What endpoint was vulnerable to a brute-force attack?**

- ``/rest/user/login``

**What endpoint was vulnerable to SQL injection?**

- ``/rest/products/search``

**What parameter was used for the SQL injection?**

- ``q``

**What endpoint did the attacker try to use to retrieve files? (Include the /)**

- ``/ftp``

* * * 

##  Task 3 Stolen data

Look carefully at:

   - The attacker's movement on the website
   - Response codes
   - Abnormal query strings

**What section of the website did the attacker use to scrape user email addresses?**

- Product Reviews

**Was their brute-force attack successful? If so, what is the timestamp of the successful login? (Yay/Nay, 11/Apr/2021:09:xx:xx +0000)**

- [11/Apr/2021:09:16:31 +0000] "POST /rest/user/login HTTP/1.0" **200** 831 "-" "Mozilla/5.0 (Hydra)"

**What user information was the attacker able to retrieve from the endpoint vulnerable to SQL injection?**

- email, password

**What files did they try to download from the vulnerable endpoint? (endpoint from the previous task, question #5)**

- coupons_2013.md.bak, www-data.bak

**What service and account name were used to retrieve files from the previous question? (service, username)**

- ftp, anonymous

**What service and username were used to gain shell access to the server? (service, username)**

- ssh, www-data

* * * 

