---
title: Walkthrough - Phishing Emails 5
published: true
---

Tags: Phishing, email, SOC analyst.
Description: Use knowledge attained to analyze a malicious email.
Difficulty: Easy
URL: [https://tryhackme.com/room/phishingemails5fgjlzxc](https://tryhackme.com/room/phishingemails5fgjlzxc)

* * *

## Notes

```

```

* * * 

## What is the email's timestamp? (answer format: mm/dd/yyyy hh:mm)

- `06/10/2020 05:58`

* * * 

## Who is the email from?

- `Mr. James Jackson`

* * * 

## What is his email address?

- `info@mutawamarine.com`

* * * 

## What email address will receive a reply to this email? 

- `info.mutawamarine@mail.com`

* * * 

## What is the Originating IP?

- `192.119.71.157`

* * * 

## Who is the owner of the Originating IP? (Do not include the "." in your answer.)

- `whois 192.119.71.157`
- `hostwinds llc`

* * * 

## What is the SPF record for the Return-Path domain?

We can use [https://dmarcian.com/](https://dmarcian.com/) for this
- `v=spf1 include:spf.protection.outlook.com -all`

* * * 

## What is the DMARC record for the Return-Path domain?

Same as abole use dmarcian.com
- `v=DMARC1; p=quarantine; fo=1`

* * * 

## What is the name of the attachment?

- `SWT_#09674321____PDF__.CAB`

* * * 

## What is the SHA256 hash of the file attachment?

- save the file somewhere then use
- `sha256sum`
- `2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f`

* * * 

## What is the attachments file size? (Don't forget to add "KB" to your answer, NUM KB)

[https://www.virustotal.com/gui/file/2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f/details](https://www.virustotal.com/gui/file/2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f/details)

* * * 

## What is the actual file extension of the attachment?

- `RAR`

* * * 

