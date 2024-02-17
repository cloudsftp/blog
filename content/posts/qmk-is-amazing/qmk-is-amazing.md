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

## Using QMK for the first time (successfully)

While configuring the keyboard with [Oryx, the ZSA configurator](https://configure.zsa.io/), I grew more and more frustrated.
Since setting the color of the LEDs is very tedious.
In Germany we call such tools "Klickibunti".
I tried to configure my other split keyboard with QMK in C directly some time ago, but couldn't quite figure it out...
But this time I had better motivation, so I tried again.

todo: introduction use case first then discovery of macros

I actually succeeded! (todo: styling)
I styled my RGB like I wanted and configured an extra layer to move applications between workspaces.
Up until now i had a layer with a numpad layout and when activating that layer, the keyboard was configured to send the meta modifier with it.
Thus telling awesome to switch to that workspace.
When I wanted to move an application, i had to press the shift key additionally.
But now I had an extra layer for moving applications, which made it a lot more pleasant.

But it still was not ideal and now I was hooked.
Sometimes, I open a GUI application from the command line, for example if i installed a new program and want to test it before setting up the `.desktop` file.
The new window then opens next to my terminal, but I don't need the terminal then.
So I move the new window to another workspace and then switch to that workspace.
With this new setup, i still need to press four buttons to do this manneuver.
This could be automated, I thought.

## The discovery of macros

To accomplish this, i simply need to send the same key code with different modifiers twice.
So I looked up how to send different key codes in sequence in QMK and found [macros](https://github.com/qmk/qmk_firmware/blob/master/docs/feature_macros.md#using-macros-in-c-keymaps).
This enables me to do exaclty what I want to do.
!
This kind of blew my mind, you can execute almost arbitrary C code on any key event and send a sequence of key codes to the computer.

So I added another layer for working with workspaces with a numpad layout.
It gets activated when pressing the keys for the other two workspace layers at the same time.
Now I have three layers for switching workspaces:
- one for switching workspaces,
- one for moving windows to workspaces,
- and one for moving and switching in one go

todo: insert code here (only third layer)


The next day, I showed this feature of my new keyboard to a colleague that also uses QMK for his keyboard.
But he was only mildly impressed.
I remember saying: "Since you can execute arbitrary C code, the possibilities are endless!"
To which he replied: "Well but what use cases are there besides fun and games."
And I mean he is right.

At least so I thought until I discovered a new use case:

## Generating random UUIDs

Generating UUIDs (todo: style)

My new job sometimes requires me ro generate random UUIDs for twsting purposes.
Using a browser is very tedious.
I have ro switch workspaces, open the website if it's not open yet, reload the page, copy the UUID, switch to the previous workspace, paste it awkwardly with Ctrl-Shift-V.
Using `uuidgen` is slightly more convenient, but still annoying.

I want something way better, generating UUIDS directly in my keyboard.
This does not have any requirement for the PC it is used with and is therefore super portable.

Set out to do it.
Implemet the uuid generation code - relatively ez.
My first implementation was trash and returned gibberish, as well as being not random.
There are two problems now:
1. My implementation of generating UUIDs is wrong and
2. I can't use time.h from the C standard library for entropy.

The first problem is easy to fix - just implement it in a way to run it on the PC to iterate fast and also enable me to do `println`-debugging.
The second problem is a lot nore fundamental.
For a second I thought, I had no way to use time as entropy.

Solving the first problem was relatively easy, as i had suspected.
I moved the code to its own [library](https://github.com/cloudsftp/qmk-zsa), wrote a main function and started debugging.
The lib was ready before long - but I still needed to get entropy from my keyboard.

After some searching I found [timer.h of TMK](https://github.com/tmk/tmk_keyboard/blob/master/tmk_core/common/timer.h).
For testing purposes, started the timer just before reading it for the seed.
Sometimes the uuid string started a few characters later - this was a big success for me, since it hinted at rhe fact that I can get entropy this way.
I just need to start the timer earlier.
Ideally once, when the keyboard starts up.
So I did exactly that and dound a nice place to put `timer_init()` where it gets executed once on startup and does not kill my keyboard (yes I put it in the wrong place once and the keyboard stopped working).

You can use the code I wrote too!
I put it in a [library](https://github.com/cloudsftp/qmk-uuid) with some hints at how to use it on your keyboard.
