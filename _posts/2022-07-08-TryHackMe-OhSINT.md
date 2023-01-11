---
title: Walkthrough - OhSINT
published: true
---

Use open source intelligence to solve this challenge.

* * *

## What Information Can We Get With One Photo?

![0xskar](/assets/WindowsXP.jpg)

* * *

## What is this users avatar of?

- ``exiftool WindowsXP.jpg``

```shell
ExifTool Version Number         : 12.42
File Name                       : WindowsXP.jpg
Directory                       : .
File Size                       : 234 kB
File Modification Date/Time     : 2022:07:08 13:33:04-07:00
File Access Date/Time           : 2022:07:08 13:33:04-07:00
File Inode Change Date/Time     : 2022:07:08 13:33:04-07:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
XMP Toolkit                     : Image::ExifTool 11.27
GPS Latitude                    : 54 deg 17' 41.27" N
GPS Longitude                   : 2 deg 15' 1.33" W
Copyright                       : OWoodflint
Image Width                     : 1920
Image Height                    : 1080
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 1920x1080
Megapixels                      : 2.1
GPS Latitude Ref                : North
GPS Longitude Ref               : West
GPS Position                    : 54 deg 17' 41.27" N, 2 deg 15' 1.33" W
```

1. Google OWoodflint to get [twitter page](https://twitter.com/owoodflint?lang=en)

* * *

## What city is this person in?

- Our hint is ``BSSID + Wigle.net``
- **BSSID** - this is the globally unique MAC address of the device
- Luckily OWoodflint left his BSSID on his twitter page ``B4:5D:50:AA:86:41``


* * *

## Whats the SSID of the WAP he connected to?

- SSID - the Server-Side Identifier. This is the name that identifies the Access Point (AP, WAP)
- UnileverWiFi

* * *

## What is his personal email address?

- googling find their gmail: [https://github.com/OWoodfl1nt/people_finder](https://github.com/OWoodfl1nt/people_finder)
- OWoodflint@gmail.com

* * *

## What site did you find his email address on?

- github

* * *

## Where has he gone on holiday?

- https://oliverwoodflint.wordpress.com/author/owoodflint/
- New York

* * *

## What is this persons password?

- Look at the whitespace on their blog

* * *
