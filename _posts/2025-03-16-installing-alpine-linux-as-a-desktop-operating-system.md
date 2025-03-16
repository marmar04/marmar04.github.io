---
title: "Installing Alpine Linux as a desktop operating system"
date: 2025-03-16
tags: [alpine linux, linux, howto]
---

## What is Alpine Linux?
Alpine Linux is a lightweight Linux distribution that is mainly used for containerisation (e.g. docker) due to its lightweight nature. Unlike traditional Linux distributions, Alpine doesn't use [systemd](https://systemd.io/) for its init system, instead using [openrc](https://wiki.gentoo.org/wiki/Project:OpenRC) as its more minimal; it also uses [musl](https://musl.libc.org/) instead of [glibc](https://www.gnu.org/software/libc/) for its implementation of [C standard library](https://en.wikipedia.org/wiki/C_standard_library) (which introduces incompatibility with proprietery software). Other than that, by default it will only install the binary without the manual (man pages) in the case of CLI applications. These and other factors make alpine very lean and lightweight.

## Why use it for desktop?
There are several reasons that one might consider using Alpine for their desktop operating system. Among others might be the lightweight nature of it, making it more appealing for older hardware, while still using something somewhat mainstream. Although there are specific Linux distributions that target older hardware, they come at a cost of not having a comprehensive wiki such as [archwiki](https://wiki.archlinux.org/title/Main_page). Alpine for example has [its own wiki](https://wiki.alpinelinux.org/wiki/Main_Page) (albeit not as comprehensive as archwiki), this has the added advantage of having a central place to look up for features and troubleshooting.

## How to install it (opiniated)

### Warning before installation
The installation steps assume that you have a working _Ethernet_ connection, Alpine is so lean it doesn't come with wifi firmware in its installation media, that would need to be set up independently once Alpine is installed to disk. I don't know how to work around this limitation (would probably need to search up on this).

### Get installation media
First of all, you will need to download the [Alpine ISO](https://alpinelinux.org/downloads/), there are several versions of the alpine ISO available, but choosing 'standard' is usually fine. The other ISOs are catered for SBCs and routers, there is no need to worry about those.

### Flash ISO to USB flash drive
Next grab a USB flash drive and flash the ISO into it. This step varies by operating system, though on Windows you can use [Rufus](https://rufus.ie/en/).

### Boot computer to USB flash drive
Now you would need to make sure you are booting using the flash drive instead of the drive inside the computer by opening the BIOS.

### Install alpine (minimal)
At this stage, what we want is essentially a base installation for us to build on top off. This installation is very barebones, as it's done in the command line, and would not install any gui components (that would be done in the next step).

The username for the iso would be `root`, no password needed. After that you would be dropped into the shell and ready to install Alpine.

It's just running [`setup-alpine`](https://wiki.alpinelinux.org/wiki/Alpine_setup_scripts#setup-alpine) in the terminal and follow the instructions, and done! You would probably want to use either `sys` or `lvmsys` for installation. You could customise the filesystem by running [`setup-disk`](https://wiki.alpinelinux.org/wiki/Alpine_setup_scripts#setup-disk) with certain environment variables.

### Setup GUI (plasma6)
After installing Alpine on disk, reboot the computer without the USB flash drive connected and Alpine should be booted. You'd be dropped into the CLI shell. Then run `setup-desktop` and choose `plasma` as the desktop environment of choice.

### Setup zram
To setup [zram](https://en.wikipedia.org/wiki/Zram), you would need to install `zram-init` and add the init service to boot. https://wiki.alpinelinux.org/wiki/Zram would have the instructions for setting it up.

### Setup networking (networkmanager)
If you remember installing Alpine initially, you might remember that we have already setup networking, [how else are you going to install packages otherwise](https://stackoverflow.com/a/72318682). However, this is the `networking` init service, which is not the usual networking tools that desktop environments are familiar with (realise that plasma doesn't show the ethernet symbol like in other distributions). For that you'd need to setup [NetworkManager](https://www.networkmanager.dev/) so that plasma can handle your internet connection. This guide assumes the usage of [`iwd`](https://archive.kernel.org/oldwiki/iwd.wiki.kernel.org/) for the backend, as suppose to the usual [`wpa_supplicant`](https://w1.fi/wpa_supplicant/)

First of all, install the needed firmware with `apk add linux-firmware`. After that, follow the instructions outlined in the [wiki](https://wiki.alpinelinux.org/wiki/NetworkManager), noting to skip the `wpa_supplicant` part and also removing the `networking` service with `rc-update del networking boot` (this step is mentioned for `wpa_supplicant` but not for iwd for some reason).

### Put iwd to runlevel async
In my usage, `iwd` cannot start properly on boot due to a `dbus` error, maybe due to installing Alpine on a HDD. Putting the problematic service to another runlevel [as done in this blog post](https://ptrcnull.me/posts/openrc-async-services/) solves this problem, as this runlevel will wait for everything in the default runlevel to finish starting up, including `dbus`. Just substitute `chronyd` with `iwd` in the blog post.

### Finishing touches
Now the base setup is mostly done. Now you can install all the other software that you need using `apk add $package` (remember that Alpine is lean? It doesn't even install all of the KDE suite, so you have to install those separately). `apk search $package` to search, and `apk list $package` to see if a package is already instaled. [Flatpak](https://flatpak.org/setup/Alpine) can also be setup if you want to install applications that way.

## Ending words
This guide is mostly for future me who never bothered to write documentation or steps needed to be done. Though it might not be the most comprehensive guide out there, it is _technically_ a proper guide (granted you follow the links for the stuff that is glossed over). Also, this blog hasn't had a post for several years so I thought this would be a good icebreaker for me to write.
