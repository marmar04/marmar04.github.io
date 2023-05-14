---
title: "swaylock unlocking issue"
date: 2023-05-14
---

# swaylock unlocking issue

## Introduction
I have setup my hyprland desktop to my liking but between that and a usable experience for me is the screen locking feature. Whenever I `swaylock` in the terminal, I can never get back unless killing the swaylock process due to some arcane reason.

I started debugging it by starting the `swaylock` command in a tmux session because you can attach it in the tty later to see the logs. Apparently I have the following logs when I enter my password:
```
2023-02-09 02:33:49 - [pam.c:102] pam_authenticate failed: invalid credentials
```

## Searching up the solution
With the help of the error and some good ol' googling, I came across an (issue in github)[https://github.com/swaywm/sway/issues/3631] that has the same problem. The issue stems from a missing file in `/etc/pam.d/swaylock` which I guess is an instruction for the pam module to authenticate the password. It should suffice for one to put the following lines in `/etc/pam.d/swaylock` using any editor of their choosing.

File contents of `/etc/pam.d/swaylock`:
```
auth include login
```

## Applying the solution in NixOS
While just writing the file directly using vim and rebooting seems like the fastest solution, I wanted to declare in my nixos config so that it creates the file with that contents each time I change laptops. For this we just need an option called `environment.etc` which you might have guessed, creates a file in `/etc`. Here's my configuration for that issue:

```nix
  environment = {
    etc = {
      "pam.d/swaylock" = {
        enable = true;
        text = ''
          auth include login
        '';
      };
    };
  };
```

Now the only thing left to do is do a `sudo nixos-rebuild boot` and `swaylock` should unlock without problems.

