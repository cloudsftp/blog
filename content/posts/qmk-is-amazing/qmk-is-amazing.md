---
title: "QMK is amazing"
date: 2024-2-5:10:42+02:00
draft: true
toc: false
images:
tags:
  - QMK
  - Awesome
---

- New keyboard
- now w rgb
- got sick of klickie-buntie configurator while configuring colors
- tried to compile keymap with QMK for the second time
- got it working and configured my new keymap

- one layer switching to workspace numpad-like
- new: one layer moving window to workspace numpad-like (awkward before bc of shift)

then i thought:
It would be nice to hold down both keys to execute both things one after the other:
Move the window to the workspace and then immeadiately jump to that workspace.

Looked up sending two key strokes after one another and found [this](https://github.com/qmk/qmk_firmware/blob/master/docs/feature_macros.md#using-macros-in-c-keymaps) in the docs.
Exactly what I was looking for.
This kind of blew my mind, you can execute almost arbitrary C code on any key event and then build a sequence of key events to send to the computer.

- Started off with minimal working prototype
- Move to function
- Would love to use macro, would not give the benefit I want - Need to look into Meta-programming
- Implement for all 10 workspaces
- beautify
- finished!!!

