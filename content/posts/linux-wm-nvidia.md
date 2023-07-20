---
title: "Linux Wm Nvidia"
date: 2023-07-20T22:27:13+02:00
tags: ["NVIDIA"]
draft: true
---

# The Thing With Window Managers

pic

## A Presentation as an Anectode

- guy at presentation was using i3 or sum
- didnt play well with the beamer
- he had to send  an email to one of the supervisors and use his laptop

Later talk:
- Yeah I enjoy WMs but you have to to everything yourself
- He was mad, told me it makes him more productive
- I said yeah but I myself enjoy a DE with a script for tiling

I was using KDE Plasma and Bismuth for tiling.

## Desktop Environments Break Too

- Bismuth not working properly
- When I wanted to be productive
- jumped the shark and installed awesome wm

3h later the WM was usable, but the problems were aparrent immeadiately.

Problems:
- volume control, play/pause (installed plugin didn't work, now only solved volume control keys myself)
- mpv not completely fullscreen


## NVIDIA Problems

Today went biking, came back to a system that doesn't boot at all.

- This afternoon updated `/etc/X11/xorg.xonf` directly from NVIDIA settings w/o backup (to fix screen tearing). BIG MISTAKE
- X server didn't start
- got it working again by reconfiguring xorg

Did most of the work on KDE. Bismuth now works again -.-

## Are DEs better?

It depends

- NVIDIAs fault X didn't start
- Awesome a lot snappier
- But a lot of work
- Can be fun

