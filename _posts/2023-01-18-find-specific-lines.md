---
title: Find Specific Lines in Bash
date: 2023-01-19 04:20:00 -0500
categories: [Resources, Bash, Walkthrough]
tags: [Bash, Scripting, Linux, shell, leetcode, grep, egrep, sed, awk]
---

## grep phone numbers

Given a text file file.txt that contains a list of phone numbers (one per line), write a one-liner bash script to print all valid phone numbers.

You may assume that a valid phone number must appear in one of the following two formats: (xxx) xxx-xxxx or xxx-xxx-xxxx. (x means a digit)

You may also assume each line in the text file must not contain leading or trailing white spaces.

**Example:**

Assume that file.txt has the following content:

```bash
987-123-4567
123 456 7890
(123) 456-7890
0(001) 345-0000
```

Your script should output the following valid phone numbers:

```bash
987-123-4567
(123) 456-7890
```

- `grep -E '^\([0-9]{3}\) [0-9]{3}-[0-9]{4}$|^[0-9]{3}-[0-9]{3}-[0-9]{4}$' file.txt`

This uses the `grep` command with the `-E` option (or `egrep`) to enable extended regular expressions. The regular expression `^\([0-9]{3}\) [0-9]{3}-[0-9]{4}$|^[0-9]{3}-[0-9]{3}-[0-9]{4}$` matches lines that match either of the two valid phone number formats.


## Print a specific line 

Given a text file file.txt, print just the 10th line of the file.

Example:

Assume that file.txt has the following content:

```shell
Line 1
Line 2
Line 3
Line 4
Line 5
Line 6
Line 7
Line 8
Line 9
Line 10
```

Your script should output the tenth line, which is:

```shell
Line 10
```

Note:
1. If the file contains less than 10 lines, what should you output?
2. There's at least three different solutions. Try to explore all possibilities.

### sed

| command | description |
|---------|-------------|
| `sed '10!d' file.txt` | `10!d` means delete all of the lines except line ten. |
| `sed -n '10p' file.txt` | `-n '10p'` means supress all other and print only the tenth line

Both of these commands will read through the entire file so the `sed` command come with another command `q` that will stop reading the file after the specified command. 

| command | description |
|---------|-------------|
| `sed '10!d;q' file.txt` | `10!d;q` means delete all of the lines except line ten. And stop reading after line ten. |

### awk

We can also use `awk` to output the line of a file. We can also use a command to stop processing after `{ print; exit }`

| command | description |
|---------|-------------|
| `awk 'NR==10' file.txt` | Prints the tenth line of file.txt. |
| `awk 'NR==10{ print; exit } file.txt` | Prints the tenth line, then stops the process. |

These are just a few of the commands I have come accross so far that have come in useful.

