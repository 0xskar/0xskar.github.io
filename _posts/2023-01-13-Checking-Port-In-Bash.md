---
title: Check Port Use Function
date: 2023-01-11 20:58:00 -0500
categories: [Scripting, Walkthrough, Bash]
tags: [Bash, Walkthrough, Scripting, Linux, if statement, functions]
---

## Check Port Function.

This simple bash script goes through a process of checking certain variables, to see if a port is in use before continuing.

```bash
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
				process=$((0 + 1))
			fi
		}
		if [[ $process = 0 ]]; then
			port_check
		fi
```
