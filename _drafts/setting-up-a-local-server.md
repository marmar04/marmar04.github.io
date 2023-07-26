---
title: "Setting up a local server"
date: 2023-06-19
---

## Introduction
I have a spare laptop that's been gathering dust for a few years. I plan to refurbish it to become a local homeserver for samba and plex streaming.

I tried to find a few guides regarding this topic but either it's not what I wanted to do or it gets too technical. Good news is that I have found [a YouTube video](https://www.youtube.com/watch?v=72D3MvPk3Xs) from Hardware Haven doing this exact thing on an ubuntu server. This guide is sufficient enough for me to get things started on my homeserver.

## Network setup
Obviously you'll have to install an operating system (NixOS in my case) and do the base installation. I have done that so I'll skip those. I also have setup ssh from the get go because I thought of using this laptop to access my github account. The only step that I'll need to do right now before installing and configuring the apps is to setup a static ip address in the local network.

Thankfully, that step is pretty straightforward as that laptop runs GNOME. It's just a matter of opening connection settings, clicking on the gear icon and going to the ipv4 section to change it to manual (I know, it's not as easy to follow as a few commands in the terminal, but it works so I'm not judging).
