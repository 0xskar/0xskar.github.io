---
title: Minecraft Bash Installer
date: 2023-01-11 20:58:00 -0500
categories: [Programming, Lesson, Other]
tags: [Minecraft, scripting, Linux, bash]
---

So just fooling around and wanted to make an installation script to install a Minecraft dedicated server on Linux. I've done a little research so going to try to put together some sort of an application.

## Application Structure

Going to start off with the application structure.

```bash
minecraft_installer
├── build.sh
└── dist
    ├── server.properties
	├── other server files
    └── server.jar
```

The main folder is going to contain the nessecary files to build the application, and best practice is to have a script for the package creation seperated in a `build.sh` file.

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
{: file="dist/" }

This will allow us to modify and automate the process of setting up the server. After we have configured everything we can tar for delivery.

## User Configuration 

It's time to create a script that will install and configure our `./dist` files. We can pretty much modify this script to do anything for us. 

We need to setup a script that checks for open ports as well as acceptable folders that will install our configured server files. So lets go.

```bash
install_main ()
{
	echo "Configuring server.properties"
	#Minecraft server properties
	#Thu Jan 12 03:46:57 PST 2023
	echo "enable-jmx-monitoring=false" > dist/server.properties
	echo "enable-jmx-monitoring=false"
	echo "############################################################"
	echo "#		CHOOSE PORT 										 "
	echo "############################################################"
	
	process=$((0))
	port_check ()
	{
		echo "Enter port for the server between 20000-30000 but not default: "
		read port_choice
		
		if [[ $port_choice != '^[0-9]+$' ]] && [[ $port_choice -ge 20000 && $port_choice -le 30000 ]];	then 
			echo "Checking $port_choice."
		else
			echo "Is not a valid number, or choice is empty."
				port_check
		fi
		if netstat -tuln | grep -q ":$port_choice "; then
			echo "Port $port_choice is in use."
			port_check
		else
			echo "Port $port_choice is free."
			echo "Port check completed successfully."
			process=$(($process + 1))
		fi
		sleep 0.2
	}
	# Check port is good 
	if [[ $process = 0 ]]; then
		port_check
	fi
	echo "rcon.port=$port_choice" >> dist/server.properties
	echo "rcon.port=$port_choice"
	echo "level-seed=" >> dist/server.properties
	echo "level-seed="		
	echo "gamemode=survival" >> dist/server.properties
	echo "gamemode=survival"
	echo "enable-command-block=false" >> dist/server.properties
	echo "enable-command-block=false"
	echo "enable-query=false" >> dist/server.properties
	echo "enable-query=false"
	echo "generator-settings={}" >> dist/server.properties
	echo "generator-settings={}"
	echo "enforce-secure-profile=true" >> dist/server.properties
	echo "enforce-secure-profile=true"
	echo "level-name=world" >> dist/server.properties
	echo "level-name=world"
	echo "motd=A Minecraft Server" >> dist/server.properties
	echo "motd=A Minecraft Server"
	echo "query.port=$port_choice" >> dist/server.properties
	echo "query.port=$port_choice"
	echo "pvp=true" >> dist/server.properties
	echo "generate-structures=true" >> dist/server.properties
	echo "generate-structures=true"
	echo "max-chained-neighbor-updates=1000000" >> dist/server.properties
	echo "max-chained-neighbor-updates=1000000"
	echo "difficulty=easy" >> dist/server.properties
	echo "difficulty=easy"
	echo "network-compression-threshold=256" >> dist/server.properties
	echo "network-compression-threshold=256"
	echo "max-tick-time=60000" >> dist/server.properties
	echo "max-tick-time=60000" 
	echo "require-resource-pack=false" >> dist/server.properties
	echo "require-resource-pack=false" 
	echo "use-native-transport=true" >> dist/server.properties
	echo "use-native-transport=true" 
	echo "max-players=20" >> dist/server.properties
	echo "max-players=20" 
	echo "online-mode=true" >> dist/server.properties
	echo "online-mode=true"
	echo "enable-status=true" >> dist/server.properties
	echo "enable-status=true"
	echo "allow-flight=false" >> dist/server.properties
	echo "allow-flight=false"
	echo "initial-disabled-packs=" >> dist/server.properties
	echo "initial-disabled-packs="
	echo "broadcast-rcon-to-ops=true" >> dist/server.properties
	echo "broadcast-rcon-to-ops=true"
	echo "view-distance=10" >> dist/server.properties
	echo "view-distance=10"
	echo "server-ip=" >> dist/server.properties
	echo "server-ip="
	echo "resource-pack-prompt=" >> dist/server.properties
	echo "resource-pack-prompt="
	echo "allow-nether=true" >> dist/server.properties
	echo "allow-nether=true" 
	echo "server-port=$port_choice" >> dist/server.properties
	echo "server-port=$port_choice"
	echo "enable-rcon=false" >> dist/server.properties
	echo "enable-rcon=false" 
	echo "sync-chunk-writes=true" >> dist/server.properties
	echo "sync-chunk-writes=true"
	echo "op-permission-level=4" >> dist/server.properties
	echo "op-permission-level=4" 
	echo "prevent-proxy-connections=false" >> dist/server.properties
	echo "prevent-proxy-connections=false"
	echo "hide-online-players=false" >> dist/server.properties
	echo "hide-online-players=false" 
	echo "resource-pack=" >> dist/server.properties
	echo "resource-pack=" 
	echo "entity-broadcast-range-percentage=100" >> dist/server.properties
	echo "entity-broadcast-range-percentage=100"
	echo "simulation-distance=10" >> dist/server.properties
	echo "simulation-distance=10" 
	echo "rcon.password=" >> dist/server.properties
	echo "rcon.password=" 
	echo "player-idle-timeout=0" >> dist/server.properties
	echo "player-idle-timeout=0"
	echo "force-gamemode=false" >> dist/server.properties
	echo "force-gamemode=false" 
	echo "rate-limit=0" >> dist/server.properties
	echo "rate-limit=0" 
	echo "hardcore=false" >> dist/server.properties
	echo "hardcore=false"
	echo "white-list=false" >> dist/server.properties
	echo "white-list=false"
	echo "broadcast-console-to-ops=true" >> dist/server.properties
	echo "broadcast-console-to-ops=true"
	echo "spawn-npcs=true" >> dist/server.properties
	echo "spawn-npcs=true" 
	echo "spawn-animals=true" >> dist/server.properties
	echo "spawn-animals=true" 
	echo "function-permission-level=2" >> dist/server.properties
	echo "function-permission-level=2"
	echo "initial-enabled-packs=vanilla" >> dist/server.properties
	echo "initial-enabled-packs=vanilla"
	echo "level-type=minecraft\:normal" >> dist/server.properties
	echo "level-type=minecraft\:normal"
	echo "text-filtering-config=" >> dist/server.properties
	echo "text-filtering-config=" 
	echo "spawn-monsters=true" >> dist/server.properties
	echo "spawn-monsters=true" 
	echo "enforce-whitelist=false" >> dist/server.properties
	echo "enforce-whitelist=false"
	echo "spawn-protection=16" >> dist/server.properties
	echo "spawn-protection=16" 
	echo "resource-pack-sha1=" >> dist/server.properties
	echo "resource-pack-sha1="
	echo "max-world-size=29999984" >> dist/server.properties
	echo "max-world-size=29999984"

}

build ()
{
	install_agree
	install_main
}

build
```
{: file="./build.sh" }

## Folder Check

I was going to make this installation process more abstract, but I am going to try to make a locally hoster webserver and document that where the variables will be completely customizable. 

But for now we need to create a process for the program to check for and create a valid directory. Then extract to the directory.


```bash
	# server.properties setup completed tar and extract to designated folder
	echo "server.properties setup completed."
	echo "############################################################"
	echo "#		CHOOSE FOLDER 										 "
	echo "############################################################"
}

install_final ()
{
	exists=0
	#check folder for installation
	user_folder=$(getent passwd $USER | cut -d: -f6)
	echo "Enter folder for installation."
	echo "This will be located at /tmp$user_folder"
	read install_folder
	install_dir="${user_folder}/${install_folder}"
	echo "$install_dir"
	sleep 0.2

	# check user folder 
	if [[ -d /tmp$install_dir ]]; then
		if [[ $exists -eq 0 ]]; then
			echo "The directory /tmp$install_dir already exists."
			echo "Try again."
			exists=1
		fi
		install_final
	else
		echo "installing at /tmp$install_dir"
		# create directory
		mkdir -p /tmp$install_dir
		echo "Successfully created at /tmp$install_dir"
		sleep 0.2
	fi
```
{: file="./build.sh" }

## Tar and extract

With our package and installation ready we can use use a simple but powerful command to tar and extract.

```bash
	# tar and extract to folder
	(cd ./dist && tar cv .) | (cd /tmp/$install_dir && tar xvf -)
	echo "COMPLETED"
}

build ()
{
	install_agree
	install_main
	install_final
}

build
```
{: file="./build.sh" }


Everything together should be a working script. Except it will extract the files to `/tmp` for testing purposes, which wouldn't be ideal. Anyways was good practice. 

I am going to probably work on making a web frontend to manipulate the server variables which would be cool to test some things I have learned. See if i can make it secure.
