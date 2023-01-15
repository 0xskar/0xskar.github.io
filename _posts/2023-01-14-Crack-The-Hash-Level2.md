---
title: Crack the Hash Level 2
date: 2023-01-14 05:39:00 -0500
categories: [Walkthrough, Tryhackme, CTF]
tags: [hash, hashcat, john the ripper, cracking, hash identification, haiti, wordlistctl, mentalist, CeWL, ttpassgen]
published: true
---

Tryhackme's sequel to their [crackthehash level 1](https://tryhackme.com/room/crackthehash). The last ones baby you through pretty good so lets see if this is a challenge. They say password cracking is part of a pentesters job, but I am not one, hopefully someday. It seems interesting.

![Password Cracking](/assets/hackerman.jpg)

## Hash Identification

Going to identify hashes here, different types MD5, SHA1, SHA2, SQL, hashcat has a lot of good examples, theres lots of websites to help with identification as well. Some other commands I've used are `hash-identifier`, and `hashcat`.

### Haiti

[Haiti](https://noraj.github.io/haiti/#/pages/install) is a ruby based hash identifier that can be install with `gem install haiti-hash`. You can use it just like `hashcat` the command followed by the hash. Unlike `hashcat` haiti actually gives us the for the hashcat and John the ripper codes! Which is super useful.

```bash
┌──(oskar㉿kali)-[~/Scripts]
└─$ haiti 1aec7a56aa08b25b596057e1ccbcb6d768b770eaa0f355ccbd56aee5040e02ee                
SHA-256 [HC: 1400] [JtR: raw-sha256]
GOST R 34.11-94 [HC: 6900] [JtR: gost]
SHA3-256 [HC: 17400] [JtR: dynamic_380]
Keccak-256 [HC: 17800] [JtR: raw-keccak-256]
Snefru-256 [JtR: snefru-256]
RIPEMD-256 [JtR: dynamic_140]
Haval-256 (3 rounds) [JtR: haval-256-3]
Haval-256 (4 rounds) [JtR: dynamic_290]
Haval-256 (5 rounds) [JtR: dynamic_300]
GOST CryptoPro S-Box
Skein-256 [JtR: skein-256]
PANAMA [JtR: dynamic_320]
BLAKE2-256
MD6-256
Umbraco HMAC-SHA1 [HC: 24800]
```
{: .nolineno }

## Wordlists

There are a lot of different types of wordlists for hash cracking, and we can make whatever you can dream of into a list to use for it as well. `seclists` as the command on kali linux is a ton of different lists for users, passwords, URLs, patterns, payloads, web shells, more.

[Rockyou](https://en.wikipedia.org/wiki/RockYou#Data_breach) is another one that comes with most installations, used with most CTFs.

### wordlistctl

[wordlistctl](https://github.com/BlackArch/wordlistctl) is a python script that fetches, installs, updates, and searches for wordlist archives from different websites with more than 6400 avalable. It also has a lot of good functions. You can also use this to search online and your system for wordlists. Check out the `-h` functions for `search` `list` and `fetch`

| Function | Outcome |
|----------|---------|
| `search <term>` | searches for term online |
| `search -l <term>` | searches for list locally |
| `fetch <term>` | downloads wordlists |
| `fetch <term> -d` | decompresses a locally stored .tar | 
| `list -g <term> ` | search for a specific category |

I have also setup an alias in my .zshrc for quick access. That way only have to use `wlctl`.

```bash
alias wlctl='python3 ~/Scripts/wordlistctl/wordlistctl.py'
{: file="~/.zshrc" .nolineno }
```

## Cracking Modes & Rules

There are several modes of cracking we can use

1. Wordlist mode, which consist in trying all words contained in a dictionary. For example, a list of common passwords, a list of usernames, etc.
2. Incremental mode, which consist in trying all possible character combinations as passwords. This is powerful but much more longer especially if the password is long.
3. Rule mode, which consist in using the wordlist mode by adding it some pattern or mangle the string. For example adding the current year, or appending a common special character.

So we can use custom wordlists then apply rules to them which saves us time than having to recreate entire wordlists. Some tools like john have custom wordlists installed but we can also create our own following the [proper rules syntax](https://www.openwall.com/john/doc/RULES.shtml).

### Mutation Rules

The main idea of rules is that they mute the wordlists to follow a rule we want them to. There are the main concepts of mutation rules.

| Rule | Idea/Usecase |
|------|--------------|
| Border mutation | combinations of digits and special added at the end or at the beginning, or both |
| Freak mutation | letters are replaced with similarly looking special symbols |
| Case mutation | the program checks all variations of uppercase/lowercase letters for any character |
| Order mutation | character order is reversed |
| Repetition mutation | the same group of characters are repeated several times |
| Vowels mutation | vowels are omitted or capitalized |
| Strip mutation | one or several characters are removed |
| Swap mutation | some characters are swapped and change places |
| Duplicate mutation | some characters are duplicated |
| Delimiter mutation | delimiters are added between characters |

We can create custom rules and keep them inside a `john-local.conf` files located inside the same directory as `john.conf`. 

A rule that uses simple border mutation by appending all 2 digits in conbinations at the end of each password: 

```bash
[List.Rules:OSKAR01]
$[0-9]$[0-9]
```
{: file="/etc/john/john-local.conf" }

And an example for using the hash to crack `2d5c517a4f7a14dcb38329d228a7d18a3b78ce83`

```bash
john hash.txt --format=Raw-SHA1 --wordlist=/usr/share/seclists/Passwords/Common-Credentials/10k-most-common.txt --rules=OSKAR01
```

## Custom Wordlist Generation

Rules are generally the better way to go to save space and time but there are instances on where custom wordlists are appreciated.

- Wordlist re-use, creating a fresh custom one can save computation power than using a mutation rule.
- We can use the wordlist easily with other tools.
- Having to use a tool that only supports wordlists, but not rules.
- Rules syntax is complicated.

### Mentalist

[Mentalist](https://github.com/sc0tfree/mentalist) is a tool at our disposal. We can import a wordlist, add some Case, Substitution, Append/Prepend rules. It can make some pretty big files though so rules save a lot of space. It is pretty good for putting a few lists together and simple rules though.

### CeWL

[Custom Word List generator](https://github.com/digininja/CeWL) Powerful web crawler that will crawl a site by 2 directories default and create a custom wordlist. Behaviours can be changed with arguments. But it can also pass onto other domains so have to be careful.

> `cewl https://example.org -d 2 -w example.txt` will scan example.org to a depth of 2 and write the results to example.txt
{: .prompt-info }

### TTPassGen

With [TTPassGen](https://github.com/tp7309/TTPassGen) we can create wordlists from scratch.

| command | outcome |
|---------|---------|
| `ttpassgen --rule '[?d]{4:4:*}' pin.txt` | generate a list of all 4 digit number combinations. |
| `ttpassgen --rule '[?l]{1:3:*}' abc.txt` | generate a list of all lowercase characters between 1-3 chars. |
| `ttpassgen --dictlist 'pin.txt,abc.txt' --rule '$0[-]{1}$1' combination.txt` | create a new wordlist that is a combination of several wordlists. |

> Generating combined wordlists can create massive files. The combination.txt above creates a file 1.64 GB for example.
{: .prompt-danger }

## Crack Hashes

We have to crack a series of hashes. Each hash has a scenario that will suggest rules. We will have to build a wordlist or use specialized data to crack the hash.

### b16f211a8ad7f97778e5006c7cecdf31

![Crack the hash 1](/assets/crackthehash02.png)

```bash
┌──(oskar㉿kali)-[~/Documents/thm/crackthehash2]
└─$ haiti b16f211a8ad7f97778e5006c7cecdf31
MD5 [HC: 0] [JtR: raw-md5]
LM [HC: 3000] [JtR: lm]
NTLM [HC: 1000] [JtR: nt]
MD2 [JtR: md2]
MD4 [HC: 900] [JtR: raw-md4]
Haval-128 (4 rounds) [JtR: haval-128-4]
Lotus Notes/Domino 5 [HC: 8600] [JtR: lotus5]
Skype [HC: 23]
IPB 2.x (Invision Power Board) [HC: 2810]
Keyed MD5: RIPv2, OSPF, BGP, SNMPv2 [JtR: net-md5]
RIPEMD-128 [JtR: ripemd-128]
Snefru-128 [JtR: snefru-128]
Domain Cached Credentials (DCC), MS Cache [HC: 1100] [JtR: mscash]
Domain Cached Credentials 2 (DCC2), MS Cache 2 [HC: 2100] [JtR: mscash2]
DNSSEC (NSEC3) [HC: 8300]
RAdmin v2.x [HC: 9900] [JtR: radmin]
Umbraco HMAC-SHA1 [HC: 24800]
Bitcoin WIF private key (P2PKH), compressed [HC: 28501]
Bitcoin WIF private key (P2PKH), uncompressed [HC: 28502]
```

#### Information

- His name is John Neige (british name?)
- Password is usually the name of his son
- Use border mutation. 
- Combination (so two) of digits and special symbols at the end or beginning, or both.

Need a wordlist of british names

```bash
wordlistctl search british
wordlistctl fetch -d british
```

Border mutation is a combinations of digits and special symbols can be added at the end or at the beginning, or both.

We can create a rule for this first.

```bash
[List.Rules:OSKAR01]
$[0-9]$[0-9]
```


