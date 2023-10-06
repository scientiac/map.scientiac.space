---
title: "nix"
date: 2023-07-29
tags:
- points
---


So, I installed [nix](https://nixos.org/) operating system, upon using it as a package manager for some time in my fedora installation.

## Installation
I used the Graphical ISO image to install the nix using the graphical installer but installed with no desktop. I was dropped in to the TTY and the fun began. I completely setup the distribution using just the `/etc/nixos/configuration.nix` though there are other methods like [home manager](https://nix-community.github.io/home-manager/),  [nix flakes](https://nixos.wiki/wiki/Flakes) with different nix commands like nix-shell that helps to create a nix development environment, [developing in a nix environment](thoughts/developing%20in%20a%20nix%20environment.md) makes it easier to keep track of what the dev is installing for the source to build correctly.

## Setup

#### Auto Congifured
The part which the installation configured itself:

1. User Creation  
I added the `docker` value in extra group because I plan to  use distrobox in the OS and `trash-cli` for the user packages.
```nix
# Define a user account. Don't forget to set a password with ‘passwd’.
  users.users.scientiac = {
    isNormalUser = true;
    description = "Scientiac";
    extraGroups = [ "networkmanager" "wheel" "docker" ];
    packages = with pkgs; [
    trash-cli
    ];
  };
```

2. Hardware Configuration  
Includes the `/etc/nixos/hardware-configuration.nix` file which does the system hardware configuration.
```nix
  imports =
    [ # Include the results of the hardware scan.
    ./hardware-configuration.nix
    ];
```

3. Bootloader  
Enables systemd-boot and I have added the `consoleMode` option to be `max` to get the max resolution of my bootloader.
```nix
# Bootloader.
  boot.loader.systemd-boot.enable = true;
# To enable full resolution and efi.
  boot.loader.systemd-boot.consoleMode = "max";
  boot.loader.efi.canTouchEfiVariables = true;
```

5. Version Definition  
Defines the system version that the OS is in.
```nix
  system.stateVersion = "23.05";
```

### Customizations
The system I configured at it's minimal stage:

1. SSH  
Enables the `ssh` daemon. 
```nix
# Enable the OpenSSH daemon.
  services.openssh.enable = true;
```

2. X11 for Display Managers and Xwayland  
Sets the keymap layout to us and enables x11 for login managers.
```nix
# Configure keymap in X11
  services.xserver = {
    enable = true;
    layout = "us";
    xkbVariant = "";
  };
```

3. Unfree Packages  
Allows unfree packages.
```nix
# Allow unfree packages
  nixpkgs.config.allowUnfree = true;
```

4. Internationalization  
Sets the locale and other settings to `en_US.UTF-8` and enabling [fcitx](thoughts/fcitx.md) for `m17n` that helps to type in  other languages.
```nix
# Select internationalisation properties.
  i18n.defaultLocale = "en_US.UTF-8";

  i18n.extraLocaleSettings = {
    LC_ADDRESS = "en_US.UTF-8";
    LC_IDENTIFICATION = "en_US.UTF-8";
    LC_MEASUREMENT = "en_US.UTF-8";
    LC_MONETARY = "en_US.UTF-8";
    LC_NAME = "en_US.UTF-8";
    LC_NUMERIC = "en_US.UTF-8";
    LC_PAPER = "en_US.UTF-8";
    LC_TELEPHONE = "en_US.UTF-8";
    LC_TIME = "en_US.UTF-8";
  };

# To use the keyboard for multiple languages.
  i18n.inputMethod = {
    enabled = "fcitx5";
    fcitx5.addons = with pkgs; [
      fcitx5-m17n
        fcitx5-gtk
    ];
  };
```

5. Neovim  
Enables neovim and sets it up as the default editor.
```nix
# Enable Neovim
  programs.neovim.enable = true;
# Setting Neovim as the system default editor.
  programs.neovim.defaultEditor = true;
```

6. Sway  
Enables sway and it's wrapper features.
```nix
# Enabling Sway
  programs.sway = {
    enable = true;
    wrapperFeatures.gtk = true;
  };
```

7. Login Manager (Display Manager)  
Enables the `greetdm` login manager and using the `regreet` greeter by configuring fonts and darkmode. 
```nix
# Enabling greetdm Login Manager
  services.greetd.enable = true;
  programs.regreet = {
    enable = true;
    settings = {
      GTK = {
        application_prefer_dark_theme = true;
        font_name = "FantasqueSansMNerdFont 12";
      };
    };
  };
```

8. Pipewire  
Enables pipewire, pipewire-pulse and alsa for audio.
```
# Setup Pipewire
  services.pipewire = {
    enable = true;
    alsa.enable = true;
    pulse.enable = true;
  };
```

9. Portals  
Enables xdg-desktop portals and dbus.
```
# Enable Portals
  services.dbus.enable = true;
  xdg.portal = {
    enable = true;
    wlr.enable = true;
# gtk portal needed to make gtk apps happy
    extraPortals = [ pkgs.xdg-desktop-portal-gtk ];
  };
```

10. Upower  
Enables the upower daemon for battery monitoring and sets the low and critical percentage values.
```nix
# Enable Upower daemon
  services.upower = {
    enable = true;
    percentageLow = 20;
    percentageCritical = 10;
  };
```

11. gvfs  
Enables trash and other similar protocols supported by gvfs.
```nix
# Enable trash protocol
  services.gvfs.enable = true;
```

12.  Fonts  
Enables font directory, sets the default font and installs specified font packages.
```nix
  fonts = {
    fontDir.enable = true;
    fontconfig = {
      enable = true;
      defaultFonts = { 
        monospace = ["FantasqueSansMNerdFont"];
      };
    };
    fonts = with pkgs; [
      (nerdfonts.override {fonts = ["Meslo" "FantasqueSansMono" "Mononoki" ];})
        lohit-fonts.nepali
        lohit-fonts.devanagari
        annapurna-sil
        noto-fonts
        noto-fonts-cjk
        noto-fonts-emoji
        liberation_ttf
    ];
  };
```

13. Packages  
Installs the specified packages for a minimal sway desktop.
```nix
# List of packages installed in system profile. To search, run:
# $ nix search wget
  environment.systemPackages = with pkgs; [
# terminal
     poweralertd
      coreutils-full
      starship
      jq
      gnome.gedit
      libnotify
      swaylock-effects
      cava
      swayidle
      mako
      wdisplays
      neovim
      wget
      git
      neofetch
      grim
      slurp
      brightnessctl
      pulseaudio
      xdg-utils
      btrfs-progs
      glib
      gtk3
      wayland
      wl-clipboard
      killall
# browsers and essentials
      firefox-devedition
      brave
      chromium
      gnome.nautilus
      alacritty
# icons
      gnome3.adwaita-icon-theme
# for wm
      rofi-wayland
      waybar
# For neovim
      lazygit
      fd
      nodejs
      lf
      fzf
      ripgrep
      python3
      gnumake
      libgccjit
      binutils
      gnat
      glibc
# For Rust 
      rustup
# ROS
      distrobox
# Brain Management
      obsidian
# Office
      libreoffice-fresh
# Arduino
      arduino-cli
      ];
```

14. Podman  
Enables podman for distrobox and sets required configurations to use [containerized ros](thoughts/containerized%20ros.md) and other packages not in the nixos repos.
```nix
# Enabling Podman
  virtualisation = {
    podman = {
      enable = true;

# Create a `docker` alias for podman, to use it as a drop-in replacement
      dockerCompat = true;

# Required for containers under podman-compose to be able to talk to each other.
      defaultNetwork.settings.dns_enabled = true;
    };
  };
```

15. Opengl  
Enables opengl with Dri support.
```nix
  hardware.opengl = {
    enable = true;
    driSupport = true;
    driSupport32Bit = true;
  };
```

This custom configuration installs and enables services needed for a minimal sway desktop without any customization and enabling desktop requirements like notification daemons and bars.
