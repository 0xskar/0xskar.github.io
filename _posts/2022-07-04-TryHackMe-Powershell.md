---
title: Hacking with PowerShell
published: true
---

[https://tryhackme.com/room/powershell](https://tryhackme.com/room/powershell)

Basics of PowerShell and PowerShell Scripting

![](/assets/powershell.jpg)

* * *

## [](#header-2)Task 1 - Objectives 

In this room, we'll be exploring the following concepts:

- What is Powershell and how it works
- Basic Powershell commands
- Windows enumeration with Powershell
- Powershell scripting

* * * 

## [](#header-2)Task 2 - What is Powershell? 

- Powesherll is the Windows Scripting Language, and shell enviroment.
- Powershell commands, called **cmdlets**, are written in .NET. 
- the output of cmdlets are **objects**
- this means running cmdlets allows us to perform actions on the output object (passing output from one cmdlet to another)
- The normal format of a cmdlet is represented using **Verb-Noun**; example cmdlet to list commands is called **Get-Command**

#### [](#header-4)Common Powershell Verbs

- Get
- Start
- Stop
- Read
- Write
- New 
- Out
- [Full List of Approved Verbs](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/approved-verbs-for-windows-powershell-commands?view=powershell-7)

##### [](#header-5)Answer the questions below

**What is the command to get help about a particular cmdlet(without any parameters)?**

- ``Get-Help``

* * * 

## [](#header-2)Task 3 - Basic Powershell Commands 

- ``Get-Command`` and ``Get-Help`` are our new best friends :smile:

#### [](#header-4)Using Get-Command

- ``Get-Command`` Gets all cmdlets installed on the computer - Example useage ``Get-Command Verb-*`` or ``Get-Command *-Noun``
- Running ``Get-Command New-*`` will view all the cmdlets for the verb New.

#### [](#header-4)Object Manipulation

- Since all outputs of every cmdlet is an object we can pass output to other cmdlets, and use specific object cmdlets to extract information
- We can do this with the Pipeline (``|``)
- After the pipe, powershell passes an object to the next cmdlet. 
- An object contains methods and properties
- We can think of methods as functions that can be applied to output from the cmdlet, and think of peoperties as vartiable in the output from a cmdlet. 
- To view these details pass the output of a cmdlet to the Get-Member cmdlet
- ``Verb-Noun | Get-Member`` example of running this to view members for the Get-Command is ``Get-Command | Get-Member -MemberType Method`` we can select between ``Method`` and ``-MemberType Properties``

![](/assets/powershell02.png)

#### [](#header-4)Creating Objects From Previous cmdlets

- We can manipulate objects by pulling out the peoperties from the output of a cmdlet and creating a new object with ``Select-Object``
- Example of listing the Directories and selecting mode and name

![](/assets/powershell03.png)

We can also use these flags to select information:
   - first - gets the first x object
   - last - gets the last x object
   - unique - shows the unique objects
   - skip - skips x objects

#### [](#header-4)Filtering Objects

- Wgeb retrieving output objects, we can select objects that match specific values. We can do this using ``Where-Object`` to filter based on the value of peoperties.
- The general format of using this cmdlet:
- ``Verb-Noun | Where-Object -Property PropertyName -operator Value``
- ``Verb-Noun | Where-Object {$_.PropertyName -operator Value}``

The second version uses the ``$_ operator`` to iterate through every object passed to the ``Where-Object`` cmdlet.

Where ``-operator`` is a list of the following operators:

  - Contains: if any item in the property value is an exact match for the specified value
  - EQ: if the property value is the same as the specified value
  - GT: if the property value is greater than the specified value

[A full list of operators](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/where-object?view=powershell-7.2&viewFallbackFrom=powershell-6).



* * * 