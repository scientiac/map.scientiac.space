---
title: Termux Connect (tercon)
date: 2023-10-06
tags:
  - points
---
### Requirements 

1. `openssh` should be installed  on both devices.
2. `sshd` daemon should be started on both devices.
3. To connect to `linux` on IP `192.168.1.1` on port  `80`.

``` bash
 ssh -p 80 linux@192.168.1.1
```

### Some useful commands.

1. `arp -v` to see the connected devices on the network.
2. `ifconfig wlan0` to find the ipaddress of your device.
3. `whoami` to find the username of your device
4. `nmap localhost` to see the running hosts on the localhost.
5. ` arp -a | awk '{print $2}' | tr -d "()" ` to see the IPaddress of all devices connected to the local network.

### For more functionality

1. Install `termux-api` and the `Termux:API` application to interact with android API.
2. Install `shizuku` and setup `rish` with Termux to use more functionalities provided by `ADB`.

### Dependencies

```
openssh
sshpass
fzf
gawk
nmap
arp
egrep
grep
netcat-openbsd
```

### Script
Connects termux with other devices via ssh, so `tercon`, move it to `.local/bin`
``` bash
#!/bin/bash

# ╻┏┓╻╻╺┳╸
# ┃┃┗┫┃ ┃ 
# ╹╹ ╹╹ ╹ 
# config file
config_file="$HOME/.config/tercon/config.sh"

# sourcing config file.
source $config_file

# Clearing the screen
clear
echo "Scanning..."

# ╻ ╻┏━┓┏━┓╻┏━┓┏┓ ╻  ┏━╸┏━┓
# ┃┏┛┣━┫┣┳┛┃┣━┫┣┻┓┃  ┣╸ ┗━┓
# ┗┛ ╹ ╹╹┗╸╹╹ ╹┗━┛┗━╸┗━╸┗━┛
# Variables
arg1=$1
argf=$2

# full path of the file
if [ $argf ]; then
    arg2="$(readlink -f $argf)"
fi

# Devices ip list
if [ "$iplist" == "none" ]; then
    iplist="$(arp -a | awk '{print $2}' | tr -d "()")"
fi

# ┏━╸╻ ╻┏┓╻   ┏━╸╻╻  ┏━╸
# ┣╸ ┃ ┃┃┗┫   ┣╸ ┃┃  ┣╸ 
# ╹  ┗━┛╹ ╹   ╹  ╹┗━╸┗━╸
# Making a function that sends a file
function send () {

        if [ "$password" == "none" ]; then
                invoke="scp -P $port $arg2 $whoami@"
        else
                invoke="sshpass -p "$password" scp -P $port $arg2 $whoami@"
        fi

        # Iterating over the list of ip addresses.
        for item in $iplist
        do

            # Using "netcat-openbsd" to see if the ssh port is available.
            nc -w 2 $item $port

            if [ $? -eq 0 ]
            then
                # Echoing out the available IP.
                echo "IP: $item"
                break
            fi

        done

        # Running the command to send the file
        $invoke$item:$send_path

        # Sending the file to another path.
        if [ "$send_path2" != "none" ]; then

            $invoke$item:$send_path2

        fi

}

# ┏━╸╻ ╻┏┓╻   ┏━┓┏━╸┏┓╻╺┳┓
# ┣╸ ┃ ┃┃┗┫   ┗━┓┣╸ ┃┗┫ ┃┃
# ╹  ┗━┛╹ ╹   ┗━┛┗━╸╹ ╹╺┻┛
# Making a function that connects to sftp.
function file () {

        if [ "$password" == "none" ]; then
                invoke="sftp -p $port $whoami@"
        else
                invoke="sshpass -p "$password" sftp -P $port $whoami@"
        fi

        # Iterating over the list of ip addresses.
        for item in $iplist
        do

            # Using "netcat-openbsd" to see if the ssh port is available.
            nc -w 2 $item $port

            if [ $? -eq 0 ]
            then
                # Echoing out the available IP.
                echo "IP: $item"
                break
            fi

        done

        # Running the command to connect to sftp
        $invoke$item
        
}

# ┏━┓┏━╸┏┓╻╺┳┓
# ┗━┓┣╸ ┃┗┫ ┃┃
# ┗━┛┗━╸╹ ╹╺┻┛
# If the second argument is "send" use function send.
if [ "$arg1" == "send" ]; then

    if [ -f "$arg2" ]; then
            send
            echo "Sucessfully Sent! $argf"
    else
            echo "Sending $argf failed!"
            exit
    fi
    exit

# ┏━╸╻╻  ┏━╸
# ┣╸ ┃┃  ┣╸ 
# ╹  ╹┗━╸┗━╸
# If the second argument is "file" use function file.
elif [[ "$arg1" == "file" ]]; then
    
    file
    exit

fi

# ┏┳┓┏━┓╻┏┓╻
# ┃┃┃┣━┫┃┃┗┫
# ╹ ╹╹ ╹╹╹ ╹
# The command that should run to connect to ssh.

main () {

        if [ "$password" == "none" ]; then
            invoke="ssh -p $port -o StrictHostKeyChecking=no $whoami@"
        else
            invoke="sshpass -p "$password" ssh -p $port -o StrictHostKeyChecking=no $whoami@"
        fi

        # Iterating over each line from $iplist, which consists of the IP addresses that
        # are working on the local network.
        # and cheching if the ssh port is available to connect.
        for item in $iplist
        do

        # Using "netcat-openbsd" to see if the ssh port is available.
        nc -w 2 $item $port

        if [ $? -eq 0 ]
        then
            # Echoing out the available IP.
            echo "IP: $item"
            break
        fi

        done

        # Running the command to connect to ssh
        $invoke$item

}

main

```

### Config file
Configuration file for `tercon`, move it to `.config/tercon/config.sh`
``` bash
# Username on Termux
whoami="linux"

# Password on Termux (Don't set password for better security)
# To make it ask for password to connect.
password="<password>" # Comment this line and uncomment the line below.
# password="none"

# Port in which the sshd daemon is running,
port="80"

# set if the ip is static
iplist="none"
# iplist="192.168.1.01"

# path on termux to send the file to.
send_path="tercon/recieved/"
# Second send path.
send_path2="storage/shared/Applications/Termux/shared/"
# send_path2="none"
```

### Usage

1. To connect to `ssh`, just type `tercon`.
2. To send files, type `tercon send <filename_having_no_spaces>`. Currently files with space in their names are unsupported.
3. To get into file transfer `sftp` mode, type `tercon file` , and use `get` and `put` commands to send and receive files.