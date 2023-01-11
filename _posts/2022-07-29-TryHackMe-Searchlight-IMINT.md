---
title: Walkthrough - Searchlight - IMINT
published: True
---

OSINT challenges in the imagery intelligence category

[https://tryhackme.com/room/searchlightosint](https://tryhackme.com/room/searchlightosint)

* * *

## Task 1 - Welcome to the Searchlight IMINT room! 

This room will introduce you to several topics within IMINT, among them: 

   - Getting into the right mindset and how to be analytical 
   - Visually extracting key data points from an image or video
   - Applying different tools to assist you in geolocation and answering context questions

When you have completed this room you should be comfortable applying tools and methodologies to geolocate and answer context questions based on visual intelligence alone. This room will prepare you for harder CTF challenges in this category as well as real-world geolocation work. 

- The flag format is: sl{flag} - this means that every answer needs to be submitted within the brackets, sl{your answer}. 

* * * 

## Task 2 - Your first challenge! 

![0xskar](/assets/searchlight-imint01.png)

**What is the name of the street where this image was taken?**

- sl{carnaby street}

* * * 

## Task 3 Just Google it! 

![0xskar](/assets/searchlight-imint03.png)

**Which city is the tube station located in?**

- ``underground public subway`` - sl{london}

**Which tube station do these stairs lead to?**

- ``underground public subway +circus`` - sl{picadilly circus}

**Which year did this station open?**

- sl{1906}

**How many platforms are there in this station?**

- sl{}

* * * 

## Task 4 Keep at it!  

![0xskar](/assets/searchlight-imint04.png)

**Which building is this photo taken in?**

- sl{vancouver international airport}

**Which country is this building located in?**

- sl{canada}

**Which city is this building located in?**

- sl{vancouver}

* * * 

## Task 5 Coffee and a light lunch 

![0xskar](/assets/searchlight-imint05.png)

- [https://www.facebook.com/weecoffeeshop/](https://www.facebook.com/weecoffeeshop/)

**Which city is this coffee shop located in?**

- sl{Blairgowrie}

**Which street is this coffee shop located in?**

- sl{allan street}

**What is their phone number?**

- sl{+44 7878 839128}

**What is their email address?**

- sl{theweecoffeeshop@aol.com}

**What is the surname of the owners?**

- sl{cochrane} found in the comments

* * * 

## Task 6 Reverse your thinking

![0xskar](/assets/searchlight-imint06.png)

**Which restaurant was this picture taken at?**

- sl{Katz's Deli}

**What is the name of the Bon Appétit editor that worked 24 hours at this restaurant?**

- sl{Andrew Knowlton}

* * * 

## Task 7 Locate this sculpture

![0xskar](/assets/searchlight-imint07.png)

This challenge will require you to apply some the techniques I have touched on so far: Scanning the image for visual clues, reverse image searching and Google dorking. Tools should not be your primary focus - don't underestimate how far you can get with dorking and scrolling search results. 

**What is the name of this statue?**

- sl{Rudolph the chrome-nosed reindeer}

**Who took this image?**

- sl{Kjersti Stensrud}

* * * 

## Task 8 ...and justice for all

This challenge is a step up in difficulty from the previous challenges and you shouldn't expect to solve this quickly, especially if you are new to IMINT. While you can certainly apply the techniques and tools you've used to s far, this challenge may force you to revise your thinking and your approach while you're working on solving this challenge. 

I highly recommend watching this Ted talk by Amy Herman on visual intelligence - "[A lesson on looking](https://www.youtube.com/watch?v=_jHmjs2270A)" if you want a unique view on how you perceive visual data.

![0xskar](/assets/searchlight-imint08.png)

**What is the name of the character that the statue depicts?**

- sl{lady justice}

**where is this statue located?**

- sl{alexandria, virginia} - Located in Courthouse Square, Alexandria, VA, USA, the Albert V. Bryan United States Courthouse has a statue of blind justice above its entrance.

**What is the name of the building opposite from this statue?**

- [https://www.google.com/maps/place/Courthouse+Square,+Alexandria,+VA+22314,+USA/@38.8027807,-77.0658489,18.75z/data=!4m5!3m4!1s0x89b7b1a8dd0b0467:0x76143771218d9dae!8m2!3d38.8027273!4d-77.065216](https://www.google.com/maps/place/Courthouse+Square,+Alexandria,+VA+22314,+USA/@38.8027807,-77.0658489,18.75z/data=!4m5!3m4!1s0x89b7b1a8dd0b0467:0x76143771218d9dae!8m2!3d38.8027273!4d-77.065216) - sl{the westin alexandria old town}

* * * 

## Task 9 The view from my hotel room

Geolocating videos aren't much different from geolocating images. A video is just a string of images, usually played at 24 frames(or images) per second. In other words, a video will hold a whole lot more images that can be analyzed, reversed and scrutinized by you. 

Here's a good writeup by Nixintel on a tool called FFmpeg, which will help you extract the key images from the video that you may need to solve this challenge. Download the attached video and follow Nixintel's guide!

**What is the name of the hotel that my friend stayed in a few years ago?**

- sl{Novotel Singapore Clarke Quay}

* * * 