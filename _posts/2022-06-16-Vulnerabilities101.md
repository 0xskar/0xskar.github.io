---
title: Vulnerabilities 101
date: 2022-06-16 18:32:00 -0500
categories: [Lesson, Tutorial]
tags: [vulnerabilities]
---

Understand the flaws of an application and apply your researching skills on some vulnerability databases.

<https://tryhackme.com/room/vulnerabilities101>

* * *

## Task 1 - Introduction

- What vulnerabilities are
- Why they're worthy of learning about
- How are vulnerabilities rated
- Databases for vulnerability research
- A showcase of how vulnerability research is used on ACKme's engagement

* * * 

## Task 2 - Introduction to Vulnerabilities 

NIST defines a vulnerability as “weakness in an information system, system security procedures, internal controls, or implementation that could be exploited or triggered by a threat source”.

There are arguably five main categories of vulnerabilities:

| Vulnerability | Description |
|---------------|-------------|
| Operating System | These types of vulnerabilities are found within Operating Systems (OSs) and often result in privilege escalation. |
| (Mis)Configuration-based | These types of vulnerability stem from an incorrectly configured application or service. For example, a website exposing customer details. |
| Weak or Default Credentials | Applications and services that have an element of authentication will come with default credentials when installed. For example, an administrator dashboard may have the username and password of "admin". These are easy to guess by an attacker. |
| Application Logic | These vulnerabilities are a result of poorly designed applications. For example, poorly implemented authentication mechanisms that may result in an attacker being able to impersonate a user. |
| Human-Factor | Human-Factor vulnerabilities are vulnerabilities that leverage human behaviour. For example, phishing emails are designed to trick humans into believing they are legitimate. |

##   Answer the questions below

**An attacker has been able to upgrade the permissions of their system account from "user" to "administrator". What type of vulnerability is this?**

- operating system

**You manage to bypass a login panel using cookies to authenticate. What type of vulnerability is this?**

- application logic

* * * 

## Task 3 - Scoring Vulnerabilities (CVSS & VPR) 

**CVSS Common Vulnerability Scoring System**

| Advantages of CVSS | Disadvantages of CVSS | 
|--------------------|-----------------------|
| CVSS has been around for a long time. | CVSS was never designed to help prioritise vulnerabilities, instead, just assign a value of severity. |
| CVSS is popular in organisations. | CVSS heavily assesses vulnerabilities on an exploit being available. However, only 20% of all vulnerabilities have an exploit available (Tenable., 2020). |
| CVSS is a free framework to adopt and recommended by organisations such as NIST. | Vulnerabilities rarely change scoring after assessment despite the fact that new developments such as exploits may be found. |

**VPR Vulnerability Priority Rating**

| Advantages of VPR | Disadvantages of VPR |
|-------------------|----------------------|
| VPR is a modern framework that is real-world. | VPR is not open-source like some other vulnerability management frameworks. |
| VPR considers over 150 factors when calculating risk.	| VPR can only be adopted apart of a commercial platform. |
| VPR is risk-driven and used by organisations to help prioritise patching vulnerabilities.	| VPR does not consider the CIA triad to the extent that CVSS does; meaning that risk to the confidentiality, integrity and availability of data does not play a large factor in scoring vulnerabilities when using VPR. |
| Scorings are not final and are very dynamic, meaning the priority a vulnerability should be given can change as the vulnerability ages. | Intentionally left blank. |

##   Answer the questions below

**What year was the first iteration of CVSS published?**

- 2005

**If you wanted to assess vulnerability based on the risk it poses to an organisation, what framework would you use? Note: We are looking for the acronym here.**

- VPR

**If you wanted to use a framework that was free and open-source, what framework would that be? Note: We are looking for the acronym here.**

- CVSS

* * * 

## Task 4 - Vulnerability Databases 

- [NVD Nation Vulnerability Database](https://nvd.nist.gov/vuln/full-listing)
- [Exploit-DB](http://exploit-db.com/)

##   Answer the questions below

**Using NVD, how many CVEs were submitted in July 2021?**

- 1585

**Who is the author of Exploit-DB?**

- [Offensive Security](https://www.offensive-security.com/community-projects/)

* * * 

## Task 5 - An Example of Finding a Vulnerability 

##   Answer the questions below

**What type of vulnerability did we use to find the name and version of the application in this example?**

- Version Disclosure

* * * 

## Task 6 - Showcase: Exploiting Ackme's Application 

##   Answer the questions below

**Follow along with the showcase of exploiting ACKme's application to the end to retrieve a flag. What is this flag?**

- THM{ACKME_ENGAGEMENT} 

* * * 
















