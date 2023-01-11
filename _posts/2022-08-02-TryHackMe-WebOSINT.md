---
title: Walkthrough - WebOSINT
published: true
---

OSINT, WebOSINT, OSINTStan. Conducting basic open source intelligence research on a website.

[https://tryhackme.com/room/webosint](https://tryhackme.com/room/webosint)

* * *

## Task 1 When A Website Does Not Exist 

Your job is to find as much information as you can about the website RepublicofKoffee.com.

**Spoiler alert** the website doesn't exist, and if it does by the time you read this, the website in its current form is not our target.

One way to collect information about a website without directly visiting it is to simply do a search for it.

Note: Sometimes plugging a website into the search bar will send you directly to the site. Avoid this by putting the site in quote marks. Also note that this will only return results where the full domain name is written out on the website.

Go ahead and google "RepublicOfKoffee.com" with and without quote marks, just to see what happens.

* * * 

## Task 2 Whois Registration 

**What is the name of the company the domain was registered with?**

- NAMECHEAP INC

**What phone number is listed for the registration company? (do not include country code or special characters/spaces)**

- (expired) 6613102107

**What is the first nameserver listed for the site?**

- DNS1.REGISTRAR-SERVERS.COM

**What is listed for the name of the registrant?**

- redacted for privacy

**What country is listed for the registrant?**

- panama

* * * 

## Task 3 Ghosts of Websites Past 

**What is the first name of the blog's author?**

- steve (archive.org)

**What city and country was the author writing from?**

- Gwangju, south korea (archive.org)

**What is the name (in English) of the temple inside the National Park the author frequently visits?**

- Jeungsimsa Temple (wikipedia)

* * * 

## Task 4 Digging into DNS 

- [ViewDNS.info](https://viewdns.info/)

**What was RepublicOfKoffee.com's IP address as of October 2016?**

- https://viewdns.info/iphistory/?domain=RepublicOfKoffee.com

**Based on the other domains hosted on the same IP address, what kind of hosting service can we safely assume our target uses?**

- Shared

**How many times has the IP address changed in the history of the domain?**

- 4

* * * 

## Task 5 Taking Off The Training Wheels 

heat.net is the target

**What is the second nameserver listed for the domain?**

- https://lookup.icann.org/en/lookup - NS2.HEAT.NET

**What IP address was the domain listed on as of December 2011?**

- 72.52.192.240 - https://viewdns.info/iphistory/?domain=heat.net

**Based on domains that share the same IP, what kind of hosting service is the domain owner using?**

- shared

**On what date did was the site first captured by the internet archive? (MM/DD/YY format)**

- 06/01/97 - https://web.archive.org/web/19970715000000*/heat.net

**What is the first sentence of the first body paragraph from the final capture of 2001?**

- ``After years of great online gaming, it’s time to say good-bye.``

**Using your search engine skills, what was the name of the company that was responsible for the original version of the site?**

- segasoft

**What does the first header on the site on the last capture of 2010 say?**

- Heat.net – Heating and Cooling https://web.archive.org/web/20101230184331/http://www.heat.net/

* * * 

## Task 6 Taking A Peek Under The Hood Of A Website 

**How many internal links are in the text of the article?**

- 5

**How many external links are in the text of the article?**

- 1

**Website in the article's only external link ( that isn't an ad)**

- purchase.org

**Try to find the Google Analytics code linked to the site**

- UA-251372-24

**Is the the Google Analytics code in use on another website? Yay or nay**

- nay	

**Does the link to this website have any obvious affiliate codes embedded with it? Yay or Nay**

- nay

* * *

## Task 7 Final Exam: Connect the Dots 


Commonalities - Heat.net
72.52.192.240	Lansing - United States	Liquid Web	2011-12-19

Commonalities - Purchase.org
67.43.1.187	Lansing - United States	Liquid Web	2013-04-19

- Liquid web l.l.c.

* * * 

