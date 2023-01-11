---
title: Command Injection
date: 2022-06-13 18:32:00 -0500
categories: [Lesson, Tutorial]
tags: [Command Injection, TryHackMe]
---

[https://tryhackme.com/room/oscommandinjection](https://tryhackme.com/room/oscommandinjection)

* * *

## Task 1 - What is Command Injection

This room goes over:

   - How to discover the command injection vulnerability
   - How to test and exploit this vulnerability using payloads designed for different operating systems
   - How to prevent this vulnerability in an application
   - Lastly, you’ll get to apply theory into practice learning in a Box at the end of the room.

Command injection is the abuse of an application's behaviour to execute commands on the operating system, using the same privileges that the application on a device is running with. This is also known as **Remote Code Executions** (RCE)

For example, being able to abuse an application to perform the command whoami to list what user account the application is running will be an example of command injection.

* * * 

## Task 2 - Discovering Command Injection

##   Answer the questions below

What variable stores the user's input in the PHP code snippet in this task? $title

What HTTP method is used to retrieve data submitted by a user in the PHP code snippet? GET

If I wanted to execute the id command in the Python code snippet, what route would I need to visit? /id

* * *

## Task 3 - Exploiting Command Injection

Command Injection can be detected in mostly one of two ways:

   - Blind command injection
   - Verbose command injection

| Method | Description|
|--------|------------|
| Blind | This type of injection is where there is no direct output from the application when testing payloads. You will have to investigate the behaviours of the application to determine whether or not your payload was successful. |
| Verbose | This type of injection is where there is direct feedback from the application once you have tested a payload. For example, running the ``whoami`` command to see what user the application is running under. The web application will output the username on the page directly. |

**Useful Payloads Linux**

|Payload | Description |
|--------|-------------|
| whoami |  See what user the application is running under. |
| ls | List the contents of the current directory. You may be able to find files such as configuration files, environment files (tokens and application keys), and many more valuable things. |
| ping | This command will invoke the application to hang. This will be useful in testing an application for blind command injection. |
| sleep | This is another useful payload in testing an application for blind command injection, where the machine does not have ping installed. |
| nc | Netcat can be used to spawn a reverse shell onto the vulnerable application. You can use this foothold to navigate around the target machine for other services, files, or potential means of escalating privileges. |

**Useful Payloads Windows**

| Payload | Description |
|---------|-------------|
| whoami | See what user the application is running under. |
| dir | List the contents of the current directory. You may be able to find files such as configuration files, environment files (tokens and application keys), and many more valuable things. |
| ping | This command will invoke the application to hang. This will be useful in testing an application for blind command injection. |
| timeout | This command will also invoke the application to hang. It is also useful for testing an application for blind command injection if the ping command is not installed. |


##   Answer the questions below

- What payload would I use if I wanted to determine what user the application is running as? whoami

- What popular network tool would I use to test for blind command injection on a Linux machine? ping

- What payload would I use to test a Windows machine for blind command injection? timeout

* * *

## Task 4 - Remediating Command Injection 

##   Answer the questions below

What is the term for the process of "cleaning" user input that is provided to an application? Input Sanitisation

* * * 

## Task 5 - Box: Command Injection (Deploy)

##   Answer the questions below

What user is this application running as?

Typing in the IP we see this machine is running ping. Using & we can run multiple commands. ``& whoami`` this application is www-data

What are the contents of the flag located in /home/tryhackme/flag.txt?

``& cat /home/tryhackme/flag.txt``



