## NOTE: THIS DISTRO IS AMD64 ONLY ##
## INSTALLATION BELOW ##

## THIS PROJECT IS STILL VERY EARLY AND EXPECT TO ENCOUNTER ISSUES WITH PACKAGE COMPATABILITY WITH YOUR SETUP ##

# rootforge
Rootforge GNU/Linux is a Linux Distribution based off Linux From Scratch targeted at expert users
It's mostly a base of Linux From Scratch with flatpak, wayland, sway, foot terminal, fuzzel app launcher and some more stuff. This distro is meant to be crafted by the user to be something he likes, by example you can add a package manager, install Xorg if you dont like the default wayland, you are free to do anything you like.

# If you need help compiling / installing stuff, you can go into the Linux From Scratch handbooks (i recommed version 12.4 because thats the version of LFS this distro is now based off) and follow the guide to compile it from there

# features:
- compile everything from source manually or with a custom package manager (optional)
- wayland dependent coming with very little Xorg/X11 libraries
- it's pretty easy to set up if you dont encounter any issues.
# a few known issues:
- as I said already there can be issues with vulkan, mesa or other graphical stuff. For me, everything works, I got a gtx 1060 and a i7 3770k. If you encounter any issues with graphical stuff you can recompile it for your specs.
- tarball is pretty chunky because this should be a decent base but I am working on keeping stuff light

## INSTALLATION ##
# Introduction:
1. You will need a USB stick
2. Get the **[Gentoo LiveGUI — `livegui-amd64-20260811T083102Z.iso`](https://...)**
