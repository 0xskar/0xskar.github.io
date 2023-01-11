---
title: Burp Suite Intruder
date: 2022-06-14 18:32:00 -0500
categories: [Lesson, Tutorial]
tags: [Burp, TryHackMe]
---

Learn how to use Intruder to automate requests in Burp Suite

[https://tryhackme.com/room/burpsuiteintruder](https://tryhackme.com/room/burpsuiteintruder)

* * *

## Task 1 - Introduction Room Outline 

Intruder allows us to automate requests, which is very useful when fuzzing or bruteforcing. We will be looking at how to use Intruder to perform both of these functions in conjunction with the other tools we have already covered.

* * * 

## Task 2 - Intruder - What is Intruder

Intruder is Burp Suite's in-built fuzzing tool.

##   Answer the questions below

**Which section of the Options sub-tab allows you to control what information will be captured in the Intruder results?** Attack Results

**In which Intruder sub-tab can we define the "Attack type" for our planned attack?** Positions

* * *

## Task 3 - Intruder - Positions 

Positions tell Intruder where to insert payloads. Burp will attempt to determine the most likely places we may wish to insert a payload automatically -- these are highlighted in green and surrounded by silcrows ``§``.

* * * 

## Task 4 - Attack Types - Introduction 

There are four attack types available:

   - Sniper
   - Battering ram
   - Pitchfork
   - Cluster bomb

We will look at each of these in turn.

* * * 

## Task 5 - Attack Types - Sniper 

Sniper is the first and most common attack type.

When conducting a sniper attack, we provide one set of payloads. For example, this could be a single file containing a wordlist or a range of numbers. From here on out, we will refer to a list of items to be slotted into requests using the Burp Suite terminology of a "Payload Set". Intruder will take each payload in a payload set and put it into each defined position in turn.

**If you were using Sniper to fuzz three parameters in a request, with a wordlist containing 100 words, how many requests would Burp Suite need to send to complete the attack?** 300

**How many sets of payloads will Sniper accept for conducting an attack?** 1

**Sniper is good for attacks where we are only attacking a single parameter, aye or nay?** aye

* * *

## Task 6 - Attack Types - Battering Ram 

Like Sniper, Battering ram takes one set of payloads (e.g. one wordlist). Unlike Sniper, the Battering ram puts the same payload in every position rather than in each position in turn.

As a hypothetical question: you need to perform a Battering Ram Intruder attack on the example request above.

If you have a wordlist with two words in it (admin and Guest) and the positions in the request template look like this:

``username=§pentester§&password=§Expl01ted§``

**What would the body parameters of the first request that Burp Suite sends be?** username=admin&password=admin

* * * 

## Task 7 - Attack Types - Pitchfork 

Pitchfork is the attack type you are most likely to use. It may help to think of Pitchfork as being like having numerous Snipers running simultaneously. Where Sniper uses one payload set (which it uses on every position simultaneously), Pitchfork uses one payload set per position (up to a maximum of 20) and iterates through them all at once.

**What is the maximum number of payload sets we can load into Intruder in Pitchfork mode?** 20

* * * 

## Task 8 - Attack Types - Cluster Bomb

Like Pitchfork, Cluster bomb allows us to choose multiple payload sets: one per position, up to a maximum of 20; however, whilst Pitchfork iterates through each payload set simultaneously, Cluster bomb iterates through each payload set individually, making sure that every possible combination of payloads is tested.

**We have three payload sets. The first set contains 100 lines; the second contains 2 lines; and the third contains 30 lines. How many requests will Intruder make using these payload sets in a Cluster Bomb attack?** 6000

* * * 

## Task 9 - Intruder - Payloads

**Which payload type lets us load a list of words into a payload set?** Simple list.

**Which Payload Processing rule could we use to add characters at the end of each payload in the set?** Add suffix.

* * * 

## Task 10 - Box - Example

Here we just go over task files, gain access to the support portal at ``/support/login`` and prepare for our challenge.

* * * 

## Task 11 - Box - Challenge 

In the previous task, we gained access to the support system. Now it's time to see what we can do with it!

The home interface shows us a table of tickets -- if we click on any of the rows in the table, we get redirected to a page where we can view the full ticket. Looking at the URL, we can see that these pages are numbered, e.g.:
http://10.10.251.92/support/ticket/NUMBER

So, what does this mean?

The numbering means that we know the tickets aren't being identified by hard-to-guess IDs -- they are simply assigned an integer identifier.

What happens if we use intruder to fuzz the/support/ticket/NUMBER  endpoint? One of two things will happen:

   1. The endpoint has been set up correctly only to allow us to view tickets that are assigned to our current user, or
   2. The endpoint has not had the correct access controls set, which would allow us to read all of the existing tickets! If this is the case, then a vulnerability called an IDOR (Insecure Direct Object References) is present.

Let's try fuzzing this endpoint!

**Which attack type is best suited for this task?** Sniper

Configure an appropriate position and payload (the tickets are stored at values between 1 and 100), then start the attack. You should find that at least five tickets will be returned with a status code of 200, indicating that they exist.

**Either using the Response tab in the Attack Results window or by looking at each successful (i.e. 200 code) request manually in your browser, find the ticket that contains the flag. What is the flag?**

![0xskar](/assets/burpsuiteintruder1.png)

* * *

## Task 12 - Extra Mile - CSRF Token Bypass 

![0xskar](/assets/burpsuiteintruder2.png)

* * *










