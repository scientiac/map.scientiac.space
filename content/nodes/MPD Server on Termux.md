---
title: MPD Server on Termux
date: 2024-07-18
tags:
  - points
---

## Installing mpd
```bash
pkg install mpd
```

# Configuration
The config file is located on `/data/data/com.termux/files/usr/etc`

Update the `music_directory` to music directory, in my case it is `~/storage/shared/Music/`

Uncomment these:
```
# For network                              
bind_to_address   "any"
                                                                           
# And for Unix Socket                                                   
bind_to_address   "/data/data/com.termux/files/usr/var/run/mpd.socket"    
                                                                 
# This setting is the TCP port that is desired for the daemon to get assigned
# to. Normally this is 6600 but for termux this port needs to be above 8000.
port        "8600"     
```

Enable Stream:
```
audio_output {
	type		"httpd"
	name		"Web Stream"
	encoder		"vorbis"		# optional, vorbis or lame
	port		"9999"
	bind_to_address	"0.0.0.0"		# optional, IPv4 or IPv6
#	quality		"5.0"			# do not define if bitrate is defined
	bitrate		"128"			# do not define if quality is defined
	format		"44100:16:1"
	max_clients	"0"			# optional 0=no limit
}
```
## Daemonize
```bash
# if termux-services is installed
sv-enable mpd
```

## Whole Config
```
music_directory		"~/storage/shared/Music/"
playlist_directory		"~/.mpd/playlists"
db_file			"~/.mpd/database"
log_file			"~/.mpd/log"
pid_file			"~/.mpd/pid"
state_file			"~/.mpd/state"
sticker_file			"~/.mpd/sticker.sql"

bind_to_address		"any"
bind_to_address		"/data/data/com.termux/files/usr/var/run/mpd.socket"
port				"8600"

follow_outside_symlinks	"yes"
follow_inside_symlinks		"yes"

input {
        plugin "curl"
}

audio_output {
	type		"httpd"
	name		"Web Stream"
	encoder		"vorbis"		# optional, vorbis or lame
	port		"9999"
	bind_to_address	"0.0.0.0"		# optional, IPv4 or IPv6
#	quality		"5.0"			# do not define if bitrate is defined
	bitrate		"128"			# do not define if quality is defined
	format		"44100:16:1"
	max_clients	"0"			# optional 0=no limit
}

audio_output {
        type            "sles"
        name            "OpenSLES output"
        mixer_type      "software"
}
```

> Use `ncmpcpp` on linux desktop or `M.A.L.P.` on android to control and play music. (Clients)