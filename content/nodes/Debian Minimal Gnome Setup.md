---
title: Debian Minimal Gnome Setup
date: 2024-07-28
tags:
  - points
---

### First Debloat
First open `software` and uninstall all the unessential packages.

### Install Necessary Packages
```
sudo apt install git curl wget gpg
```

### Repository for Libadwaita Themes
Copy and run this code to add the repository:
```
curl -s https://julianfairfax.gitlab.io/package-repo/pub.gpg | gpg --dearmor | sudo dd of=/usr/share/keyrings/julians-package-repo.gpg

echo 'deb [ signed-by=/usr/share/keyrings/julians-package-repo.gpg ] https://julianfairfax.gitlab.io/package-repo/debs packages main' | sudo tee /etc/apt/sources.list.d/julians-package-repo.list
```

#### Installing `adw-gtk3` and `morewaita`
```
sudo apt install adw-gtk3 morewaita
```

Set the themes from `gnome-tweaks`.
### Repository for Brave
```
sudo curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main"|sudo tee /etc/apt/sources.list.d/brave-browser-release.list

sudo apt update
```

#### Installing Brave
```
sudo apt install brave-browser
```

### Repository for Visual Studio Code
```
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg

sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg

echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" |sudo tee /etc/apt/sources.list.d/vscode.list > /dev/null

rm -f packages.microsoft.gpg
```

#### Installing Visual Studio Code
```
sudo apt install apt-transport-https
sudo apt update
sudo apt install code
```
### Setting up Flatpak
```
sudo apt install flatpak
sudo apt install gnome-software-plugin-flatpak

# Enable Flathub
sudo apt install gnome-software-plugin-flatpak
```

### Theming for Gnome Consistency

#### Adwaita Firefox
Make sure firefox is launched at least once.
```
curl -s -o- https://raw.githubusercontent.com/rafaelmardojai/firefox-gnome-theme/master/scripts/install-by-curl.sh | bash
```
#### Adwaita Thunderbird
Make sure thunderbird is launched at least once.
```
git clone https://github.com/rafaelmardojai/thunderbird-gnome-theme && cd thunderbird-gnome-theme

./scripts/auto-install.sh
```
