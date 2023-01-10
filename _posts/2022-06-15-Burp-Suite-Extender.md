---
title: Burp Suite Extender
date: 2022-06-15 18:32:00 -0500
categories: [Lesson, Tutorial]
tags: [Burp, TryHackMe]
---

Learn how to use Extender to broaden the functionality of Burp Suite

[https://tryhackme.com/room/burpsuiteextender](https://tryhackme.com/room/burpsuiteextender)

* * *

## Task 1 - Introduction - Outline 

This room will focus on Burp Suite's modular aspects: the exposed functionality, which allows developers to craft extra additional modules for the framework.

* * * 

## Task 2 - Extender - The Extender Interface 

Extensions are invoked in descending order based on this list. In other words: all traffic passing through Burp Suite will be passed through each extension in order, starting at the top of the list and working down. This can be very important when dealing with extensions that modify the requests as some may counteract or otherwise hinder one another. 

##   Answer the questions below

**Familiarise yourself with the Extender management interface.**

**Are extensions invoked in ascending (A) or descending (D) order?** Descending

* * * 

## Task 3 - Extender - The BApp Store 

Look through the list of apps in the BApp store and install at least one other that catches your fancy.

* * * 

## Task 4 - Extender - Jython 

If we want to use Python modules in Burp Suite, we need to have downloaded and included the separate Jython Interpreter JAR file. The Jython interpreter is a Java implementation of Python.

Here we install Jython, and Ruby.

[Bonus Question -- Optional] Add JRuby to your Burp Suite install. A download link can be found here. The process for this is exactly the same as with Jython.

* * * 

## Task 5 - Extender - The Burp Suite API 

Nothing here.

* * * 

## Task 6 - Conclusion - Room Conclusion 

I can use Burp Suite Extender!

* * * 
