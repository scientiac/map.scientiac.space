---
title: QuickRCS
date: 2023-10-06
tags:
  - points
---


A `termux` - `android-api` script that gives a dialog and sends message to a particular person emulating key-presses using  `adb` .

## Dependencies

### Apps
Applications required to run the script.
```
termux
termux:api
termux:widget
shizuku
nova
google-messages
```

### CLI
CLI tools used in the script.
```
sed
awk
tr
cat
touch
rish
termux-dialog
adb
```

## Scripts

### Main Script
This is the script that is kept on the  `~/.shortcuts/tasks/` directory and will be executed with a gesture/key-press. 
``` bash
#!/bin/bash
## Refreshes the `sms` file to have `substitutethis` in it.
cat ~/scripts/text/smsbkp > ~/scripts/text/sms

## Invokes a dialog and extracts the text as a variable.
message=$(termux-dialog -t "Send a Message" -i "Enter Your Message Here" | awk "/text/" | tr -d \" | awk '{$1="";print}' | sed -e 's/ //')

## A variable for the word to be substituted in the `smsbkp` file,
message_to_sub='substitutethis'

## Substituting the placeholder to the actual message.
sed -i "s/$message_to_sub/$message/" ~/scripts/text/sms

## Executing `sms` script to get to the messaging page.
rish < ~/scripts/text/sms

## Wait
sleep 0.1

## Hitting `d-pad next` to reach to the send icon whick is 2 presses far.
rish < ~/rish/buttons/next

## Wait
sleep 0.1

## Hitting `d-pad next` to reach to the send icon whick is 1 press far.
rish < ~/rish/buttons/next

## Wait
sleep 0.1

## Hitting `enter` to send the message.
rish < ~/rish/buttons/enter
```

### SMS Backup
This is the file (`smsbkp`) that gets modified and set as `sms` in the previous script. It is required to send  different messages on each request. This is an `adb` command to write message in the text-box of a particular phone number.
```
am start -a android.intent.action.SENDTO -d sms:+9779876543210 --es sms_body "substitutethis" --ez exit_on_sent true
```

This is to create `sms` file if not available.
```
touch sms
```

### Buttons
These are the `keyevent`s which are used for hitting `d-pad left` and `enter` to send the message.
``` bash
# next
input keyevent 22
# enter
input keyevent 66
```

## Step By Step Instructions

1. Set up `shizuku`, and set up  `rish`  inside `termux` by following the instructions in the app under "Use Shizuku in terminal apps". Do not forget to add `rish` and the `dex` to the path after making it executable `chmod +x [filename]`.
2. Set up `termux`, install `termux:api` app and `termux-api` package by `pkg install termux-api` to access the API of android system for dialog. 
3. Install `termux: widget` and grant it the required permissions.
4. We can run commands on `rish` by`rish < filename` where 'filename' will be the file that contains a command like `input keyevent 22`  and these commands are `adb` commands so, any command that can run on an `adb shell` can run with rish.
5. Make a directory for scripts, in my case it's `~/scripts/text/` and put the command for `smsbkp` in a file called `smsbkp`.
6. Make an empty file named `sms` on the scripts directory by running `touch sms`.
7. Make a directory for key-presses, in my case it's `~/rish/buttons/` and put the respective commands for `next` and `enter` with the same filename.
8. Set up `termux:widget` by making a directory called `~/.shortcuts`  with `mkdir ~/.shortcuts` and to run the processes in the background make another directory called `~/.shortcuts/tasks/` and put the main script inside it with a desired filename, in my case it's `text`.
9. You can run it from a widget of `termux:widget` or set it as a button using any shortcut creator. I used nova and set it to a gesture.
10. At last, read the script nicely, if it fails to run then try to debug the problem yourself.