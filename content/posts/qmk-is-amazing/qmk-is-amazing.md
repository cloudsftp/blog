---
title: "QMK is awesome"
date: 2024-2-16:10:42+02:00
draft: true
toc: false
images:
tags:
  - QMK
  - Awesome
---

I got a new Keyboard!
This time with RGB.
I am not a big fan - but it is important for the following story.
So gather around.

The keyboard in question is the [ZSA Voyager](https://www.zsa.io/voyager/).
I chose this because it is made with portability in mind and I want to take it to and from work once in a while.
With my other split keyboard this was not really safe.

While configuring the keyboard with [Oryx, the ZSA configurator](https://configure.zsa.io/), I grew more and more frustrated.
Since setting the color of the LEDs is very tedious.
I tried to configure my other split keyboard with QMK in C directly some time ago, but couldn't quite figure it out...
But this time I had better motivation, so I tried again.



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


- implementing it for ergodox ez
- for some reason, send string wont compile
- test SS_TAP, wont compile either
- opt for manually setting and unsetting key instead of hunting down compiler errors


new use case discovered:
automatically generate uuidv4

Set out to do it.
Implemet the uuid generation code - relatively ez.
try to get some entropy from <time.h> in the standard library - alway returns the same thing

Need other way.
Find [timer.h of TMK](https://github.com/tmk/tmk_keyboard/blob/master/tmk_core/common/timer.h).
use it for the seed - done.

You can use it too!
[Library with documentation](https://github.com/cloudsftp/qmk-uuid)

