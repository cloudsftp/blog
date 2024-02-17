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
I am not a big fan - but it is an important detail for the following story.
So gather around.

The keyboard in question is the [ZSA Voyager](https://www.zsa.io/voyager/).
I chose this because it is made with portability in mind and I want to take it to and from work once in a while.
With my other split keyboard this was not really safe to do.

While configuring the keyboard with [Oryx, the ZSA configurator](https://configure.zsa.io/), I grew more and more frustrated.
Since setting the color of the LEDs is very tedious.
I tried to configure my other split keyboard with QMK in C directly some time ago, but couldn't quite figure it out...
But this time I had better motivation, so I tried again.

I actually succeeded! (todo: styling)
I styled my RGB like I wanted and looked around the QMK documentation.
There i found [macros](https://github.com/qmk/qmk_firmware/blob/master/docs/feature_macros.md#macros).
This was very exciting and I immediately had a use case foe rhis feature:

Sometimes, I open a GUI application from the command line, for example if i installed a new program and want to test it, before setting up the `.desktop` file.
The new window then opens next to my terminal, but I don't need the terminal then.
So i move the new window to a new workspace and then switch to that workspace.
With my new configuration this was not that awkward anymore, but those were still two key presses, which could be automated.
So I did exactly that.
Now I have three layers for switching workspaces:
- one for switching workspaces,
- one for moving windows to workspaces,
- and one for moving and switching in one go

todo: insert code here (only third layer)


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

I showed this feature of my new keyboard to a colleague that also uses QMK for his keyboard.
But he was only mildly impressed.
I remember saying: "You can execute arbitrary C code on rhis thing. The possibilities are endless!"
To which he replied: "Well but what use cases are there besides fun and games."
And I mean he is right.

At least so I thought until I discovered a new use case:

Generating UUIDs (todo: style)


Set out to do it.
Implemet the uuid generation code - relatively ez.
try to get some entropy from <time.h> in the standard library - alway returns the same thing

Need other way.
Find [timer.h of TMK](https://github.com/tmk/tmk_keyboard/blob/master/tmk_core/common/timer.h).
use it for the seed - done.

You can use it too!
[Library with documentation](https://github.com/cloudsftp/qmk-uuid)

