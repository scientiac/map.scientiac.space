---
title: "fcitx"
date: 2023-07-26
tags:
- points
---
[fcitx](https://fcitx-im.org/wiki/Fcitx_5) is a lightweight input method framework that provides environment-independent language support for Linux and Unix systems like freebsd.

I required it to set a roman transliteration to type in Nepali language.

## Installation
Two `fcitx` bundles were required for my use case:
```bash
# Package name on my distro(fedora) and probably everywhere else too
fcitx5
fcitx5-m17n
```

Then set environment variables for IM modules:
```bash
# Put this in your ~/.bash_profile or equivalent file for your shell env.
# For fcitx
export GTK_IM_MODULE='fcitx'
export QT_IM_MODULE='fcitx'
export SDL_IM_MODULE='fcitx'
export XMODIFIERS='@im=fcitx'
```

> `fcitx5` is a successor package to fcitx because the package `fcitx` is in maintenance mode.  

> `m17n` is an abbreviation of Multilingualization and hence provides support for many languages including the *indic* ones.

## Configuration
1. Run the following command to open the configuration settings:
```bash
# This package is installed automatically as a weak dependency for fcitx5.
# If it is not available first install it using your package manager.
fcitx5-configtool
```
2. Un-check the *Only Show Current Language* option and search for your desired language. Mine was *Nepali*, and I got 5 options.
```bash
# yours might vary
inscript2-deva
rom
rom-translit
trad
trad-ttf
```

I checked all the options and `rom-translit` was very easy to use. So, I stuck with it.

I am using [hyprland](https://hyprland.org/) and it crashed when I ran `fcitx -d`  fron the terminal but putting `exec-once = fcitx5 -d &` on the `hyprland` configuration file worked fine.

## Additional Tip
Just using it as default, I didn't have any icon for `rom-translit`  and looking at `/usr/share/m17n/icons` there was no `ne-rom-translit.png` . I just copied the `ne-rom.png` and renamed to `ne-rom-translit.png` there, which worked for me as an icon for the keyboard layout.

> नमस्ते !