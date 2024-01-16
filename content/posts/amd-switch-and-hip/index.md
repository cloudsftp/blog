---
title: "My switch to AMD and the problems that ensued"
date: 2024-01-01T20:59:56+02:00
draft: true
toc: false
images:
tags:
  - NVIDIA
  - AMD
  - Blender
---


As you can tell from my previous posts, I do not like NVIDIA.
That is because I had a lot of issues with the drivers in the past and recently, the card started producing black flashes (see [this post]({{< ref "/posts/nvidia-530-flickering" >}})).
So I finally made the switch to an AMD graphics card.

The setup was rather smooth.
OpenSUSE has two options for open source drivers for AMD cards and I chose the [Radeon driver](https://en.opensuse.org/SDB:Radeon).
The linked page lists a few commands that have to be run.
Only three of the commands were applicable for me.
```bash
init 3
modprobe radeon
reboot
```
I ran all of them without encountering errors and rebooted.

But then I was greeted with *this*
![tty screen](tty_screen.jpg)

My first instinct was to remove the installed NVIDIA drivers (`zypper rm nvidia-compute-G06`) and remove the NVIDIA-specific options from the GRUB configuration.
But this did not resolve my issue.
Instead, reconfiguring Xorg did finally solve my issue.

Since the config was fresh I did need to add the infamous "TearFree" option (see [this wiki entry](https://linuxreviews.org/HOWTO_fix_screen_tearing)).
After this slight hickup, I was all set.
But then, a few weeks later I wanted to render a video and I hit my first real problem with the AMD drivers...

- renders taking very long with the cycles engine on cpu
- tried to render on gpu, says it isnt supported
- stumble upon reddit post
- first time using opi to install
    - libffi_7
    - libpython3_6m1

- installing additional packages
    - hip-amd-runtime
    - hipsparselt + -devel + every asan version of already installed hip related packages
    - 

