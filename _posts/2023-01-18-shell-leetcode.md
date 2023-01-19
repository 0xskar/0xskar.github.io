---
title: Shell LeetCode - 193. Valid Phone Numbers
date: 2023-01-18 04:20:00 -0500
categories: [Resources, Bash, Walkthrough]
tags: [Bash, Scripting, Linux, shell, leetcode]
---

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