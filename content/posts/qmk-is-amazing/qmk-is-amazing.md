---
title: "QMK is awesome"
date: 2024-02-20T22:01:42+01:00
draft: false
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
In Germany, we call such tools "Klickibunti".
I tried to configure my other split keyboard with QMK in C directly some time ago, but couldn't quite figure it out...
But this time I had better motivation, so I tried again.

<div style='text-align: center; font-size: 1.5em'>
    <strong> I actually succeeded! </strong>
</div>

I styled my RGB like I wanted and configured an extra layer to move applications between workspaces.
Up until now I used a layer with a numpad-like layout and when activating that layer, the keyboard was configured to send the meta modifier with the numbers that are typed.
This was telling Awesome WM to switch to that workspace.
When I wanted to move an application, I had to additionally press the shift key.
But now I had an extra layer for moving applications, which sends both the meta and shift modifier alongside the number that is typed.
This makes moving windows between workspaces a lot more pleasant.

But it still was not ideal, and now I was hooked on playing around with QMK.
Sometimes, I open a GUI application from the command line.
For example if I installed a new program and want to test it before setting up the `.desktop` file.
The new window then opens next to my terminal.
But I don't need the terminal at that point, and it is taking up half of the space on my monitor.
So I move the new window to another workspace and then switch to that workspace.
With this new setup, I still need to press four buttons to do this maneuver.
This could be automated, I thought.

## The discovery of macros

To accomplish this, I simply need to send the same key code with different modifiers twice.
So I looked up how to send different key codes in sequence in QMK and found [macros](https://github.com/qmk/qmk_firmware/blob/master/docs/feature_macros.md#using-macros-in-c-keymaps).
This is exactly what I was looking for!
This kind of blew my mind, you can execute almost arbitrary C code on any key event and send a sequence of key codes to the computer.

So I added another layer for working with workspaces with a numpad-like layout.
It gets activated when pressing the keys for the other two workspace layers at the same time.
Now I have three layers for switching workspaces:
- one for switching workspaces,
- one for moving windows to workspaces,
- and one for moving and switching in one go

Here is the function that gets called with a string of one character that specifies the workspace to move and switch to.

```
void move_and_switch(char *workspace, keyrecord_t *record) {
    if (!record->event.pressed) {
        return;
    }
    register_code(KC_LGUI);
    register_code(KC_LSFT);
    SEND_STRING(workspace);
    unregister_code(KC_LSFT);
    SEND_STRING(workspace);
    unregister_code(KC_LGUI);
}
```

I spare you the boilerplate code that calls this function.
The gist of the implementation is setting the modifiers, sending a keystroke, removing one modifier, and sending the keystroke again.

The next day, I showed this feature of my new keyboard to a colleague that also uses QMK for his keyboard.
But he was only mildly impressed.
I remember saying: "Since you can execute arbitrary C code, the possibilities are endless!"
To which he replied: "Well but what use cases are there besides fun and games."
And I mean he is right.

At least so I thought so until I discovered a new use case:

## Generating random UUIDs

My new job sometimes requires me to generate random UUIDs for testing purposes.
Using a browser for this task is very tedious.
I have to switch workspaces, open the website if it's not open yet, reload the page, copy the UUID, switch to the previous workspace, and finally paste it awkwardly with Ctrl-Shift-V.
Using `uuidgen` is slightly more convenient, but still annoying.

I want something way better, generating UUIDs directly in my keyboard.
This does not have any requirement for the PC it is used with and is therefore super portable.
So I set out to implement it for my new keyboard.

### Day 1

After my first evening of working on this feature, my keyboard wrote `ffaffa` each time I triggered the code I just wrote.
This first implementation was trash.
There are three problems now:
1. My implementation of generating UUIDs is wrong,
2. The code is triggered twice, and
3. I can't use `time.h` from the C standard library for entropy.

The first problem is easy to fix - just implement it in a way to run it on the PC to iterate fast and also enable me to do `println`-debugging.
The second problem is also an easy fix - I just have to detect whether it is a key-down event or a key-up event and only trigger in one case.
But the third problem is a lot more fundamental.
For a second I thought, I had no way to use time as entropy.

Solving the first problem was relatively easy, as I had suspected.
I moved the code to its own [library](https://github.com/cloudsftp/qmk-zsa), wrote a main function and started debugging.
The lib was ready before long - but I still needed to get entropy from my keyboard.

### Day 2

After my second evening of working on this feature, my keyboard wrote `0000f42d-8ccf-4646-b129-5b045db47f7` each time I triggered the code.

As you can see, the UUID is now a valid UUIDv4.
But it is still not random at all.
After some searching I found [timer.h of TMK](https://github.com/tmk/tmk_keyboard/blob/master/tmk_core/common/timer.h).
But it was too late already, and I had work the next day.

### Day 3

Now it was time to put the last building block into its place.
I used `timer_read()` instead of `time(&t)` to get the seed and had to start the timer at some other point.
For testing purposes, I started the timer with `timer_init()` just before reading it for the seed.
The result was that sometimes the UUID was shifted by a few characters.

This was a big success for me, since it hinted at the fact that I can get entropy this way.
I just needed to start the timer earlier.
Ideally once, when the keyboard starts up.
So I did exactly that and found a nice place to put `timer_init()` where it gets executed once on startup and does not kill my keyboard.
(Yes I put it in the wrong place once and the keyboard stopped working at one point or another)

You can use the code I wrote too!
I put it in a [library](https://github.com/cloudsftp/qmk-uuid) with some hints at how to use it on your keyboard.
For the code also check this library.
