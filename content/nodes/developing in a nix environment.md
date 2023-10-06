---
title: "developing in a nix environment"
date: 2023-08-12
tags:
- points
---
NixOS can create development environments using the default.nix file on a folder when nix-shell is executed which bundles the declared packages and requirements for the environment making it repeatable in other systems.

I was developing a gtk finger client for which I created a simple environment with `default.nix` file inside the `gtk-finger` directory.

```nix
with import <nixpkgs> {};

stdenv.mkDerivation {
  name = "finger-gtk-env";
  buildInputs = [
    gtk3
    pkgconfig
    gcc
  ];
}
```

then I ran `nix-shell` command which took me to the development environment which had the required packages for me to develop my [gtk finger client](nodes/gtk%20finger%20client.md).

then i wrote the source code for the [gtk finger client](nodes/gtk%20finger%20client.md) and compiled with the following string:
```bash
g++ -o finger-gtk gtk-finger.cpp `pkg-config --cflags --libs gtk+-3.0`
```

