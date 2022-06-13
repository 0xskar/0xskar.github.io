---
title: TryHackMe - Command Injection
published: true
---

[https://tryhackme.com/room/oscommandinjection](https://tryhackme.com/room/oscommandinjection)

Learn how to detect and exploit XSS vulnerabilities, giving you control of other visitor's browsers.

## [](#header-3)Task 1 - What is Command Injection

This room goes over:

   - How to discover the command injection vulnerability
   - How to test and exploit this vulnerability using payloads designed for different operating systems
   - How to prevent this vulnerability in an application
   - Lastly, you’ll get to apply theory into practice learning in a practical at the end of the room.

Command injection is the abuse of an application's behaviour to execute commands on the operating system, using the same privileges that the application on a device is running with. This is also known as **Remote Code Executions** (RCE)

For example, being able to abuse an application to perform the command whoami to list what user account the application is running will be an example of command injection.

## [](#header-3)Task 2 - Discovering Command Injection

**Answer the questions below**

What variable stores the user's input in the PHP code snippet in this task? $title

What HTTP method is used to retrieve data submitted by a user in the PHP code snippet? GET

If I wanted to execute the id command in the Python code snippet, what route would I need to visit? /id

## [](#header-3)Task 3 - Exploiting Command Injection

| Method | Description|
|--------|------------|
| Blind | This type of injection is where there is no direct output from the application when testing payloads. You will have to investigate the behaviours of the application to determine whether or not your payload was successful. |
| Verbose | This type of injection is where there is direct feedback from the application once you have tested a payload. For example, running the ``whoami`` command to see what user the application is running under. The web application will output the username on the page directly. |