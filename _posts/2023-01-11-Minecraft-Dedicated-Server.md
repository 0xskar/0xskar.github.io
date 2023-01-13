---
title: Minecraft Dedicated Server Setup
date: 2023-01-11 20:58:00 -0500
categories: [Minecraft, Walkthrough, Other]
tags: [Minecraft, User Management, Linux]
---

Going to go through the setup for my Minecraft dedicated server on Kali Linux. It's very easy to do.

We can do a few things here before we start the installation. Either use `ssh` to connecto to our server, or if you have physical access to the server, you don't have to worry about it

Before we get started we will setup a new user. Just in case the server is compromised it will be a little more difficult for the attacker to do anything.

```bash
sudo adduser minecraft
```

```bash
sudo passwd mcuser
```

Check to make sure java installed you need 16 >

```bash
java --version
```

swap user

```
su minecraft
```

Check to install screen

```bash
sudo apt install screen
```

cd to the users home directory and create a folder for the install

```bash
cd ~ && mkdir minecraft && cd minecraft
```

Now we need to get the config files for minecraft. Check out the [Minecraft server page](https://www.minecraft.net/en-us/download/server) and wget then install.

```bash
wget https://piston-data.mojang.com/v1/objects/c9df48efed58511cdd0213c56b9013a7b5c9ac1f/server.jar
```

```bash
java -Xmx1024M -Xms1024M -jar server.jar nogui 
```

We will get a message to accept the EULA so use nano to change the eula to `TRUE`.

Edit the `server.properties` file to you liking note the port that will be using `25565` as you may have to update your firewall to allow connections.

We can finally start the server with `screen` then use our command from earliar to launch the server.

```bash
java -Xmx1024M -Xms1024M -jar server.jar nogui 
```

Enjoy!





