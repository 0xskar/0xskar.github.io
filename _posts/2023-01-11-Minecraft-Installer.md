---
title: Minecraft Python Installer
date: 2023-01-11 20:58:00 -0500
categories: [Programming, Lesson, Other]
tags: [Minecraft, scripting, Linux, python]
published: false
---

So just fooling around and wanted to make an installation script to install a Minecraft dedicated server on Linux. I've done a little research so going to try to put together some sort of an application.

## Tools

| Tools | Description |
|-------|-------------|
| [Pyarmor](https://wiki.python.org/moin/Pyarmor) | Pyarmor is a command line tool used to obfuscate python scripts, bind obfuscated scripts to fixed machine or expire obfuscated scripts. |
| [Makeself](https://makeself.io/) | Allows creating a self-extracting distribution package in Unix systems. |
| [PyInstaller](https://pyinstaller.readthedocs.io/en/stable/usage.html) | It Bundles a Python application and all its dependencies into a single package. |

## Application Structure

Going to start off with the application structure.

```bash
minecraft_installer
├── build.sh
├── dist
│   └── server.jar
└── makeself_stage
    └── installer.sh
```

The main folder is going to contain the nessecary files to build the application, and best practice is to have a script for the package creation seperated in a `build.sh` file.

This file will have multiple steps. We will start by declaring the pyarmor path.

```bash
PYARMOR="$HOME/.local/bin/pyarmor"
```
{: file="minecraft/build.sh" }

We are going to use this file to build our dependent modules, like copying binaries and libraries to a folder.

Now we will generate distribution packages with pyarmor. This option uses pyinstaller for creating the package bundle. Also, you can use the option “-e” to pass extra options to pyinstaller

```bash
#!/bin/sh
PYARMOR="$HOME/.local/bin/pyarmor"
${PYARMOR} pack --clean -e '--paths='path_to_search_imports:.' --hidden-import='PIL._tkinter_finder' --add-binary='path_to_lib.so:.' --exclude-module=modules_not_needed --add-data='./Config:Config' --add-data='./*.png:.'' install.py
```
{: file="minecraft/build.sh" }

Pyarmor will create the default bundled package in `/dist`. We can change this by using the `-O` or output option.

Setup makeself so we can create self-extracting archives from the package folder created by pyarmor.

```bash
sudo nala install makeself
```

## Payload

So for the payload we need to create an archive of the preconfigured minecraft server files. So download the minecraft server files first.

```bash
wget https://piston-data.mojang.com/v1/objects/c9df48efed58511cdd0213c56b9013a7b5c9ac1f/server.jar
```
{: file="dist/" }

Then we need to extract the jar files then delete the server.jar

```bash
java -Xmx1024M -Xms1024M -jar server.jar nogui
```
{: file="dist/}

This will allow us to modify and automate the process of setting up the server. After we have configured everything we can tar for delivery.

## Create Package for delivery
tar -cvf serverfiles.tar *

## Installation Script 

It's time to create the installation script that will install the `dist/server.jar` file. We can pretty much modify this script to do anything for us. 

```bash

function install_file()
{ 
   echo "Extracting the application package"
   if [ -d $HOME/minecraft] 
   then
     echo "Application already installed." 
     exit 1
   else
     mkdir $HOME/minecraft
   fi
   
    #install the package


    #config server files


}
```
{: file="makeself_stage/installer.sh" }





## Makeself

With our package and installation ready we can use makeself to package.

```bash
makeself ./minecraft_installer/makeself_stage/
```
{: file=/minecraft_installer}



