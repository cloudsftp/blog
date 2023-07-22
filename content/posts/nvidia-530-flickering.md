---
title: "Black Flickering in Proprietary NVIDIA Drivers 530"
date: 2023-07-22T10:37:07+02:00
draft: true
toc: false
images:
tags:
  - NVIDIA
---

- black flickers
- add parameter to grub `/etc/default/grub` thanks https://forums.developer.nvidia.com/t/very-short-black-flickering-flashes-with-nvidia-2080ti-since-nvidia-530-41-03-driver/248354/13
- rebuild grub
    - openSuse: `sudo grub2-mkconfig -o /boot/grub2/grub.cfg`

