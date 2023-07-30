---
title: "The Thing With Window Managers"
date: 2023-07-21T22:27:13+02:00
draft: false
toc: false
tags: 
    - Window Manager
    - AwesomeWM
    - NVIDIA
---

{{< image src="/posts/awesome-wm-init/initial.png" alt="Default Awesome WM configuration on OpenSUSE" position="center" >}}

## Presenting with Personal Laptops

Last semester, I was part of a seminar where every participant had to do a presentation.
Everybody had to use their own laptops to display the slides on the projector.
This was done for the convenience of the supervisors, presumably.
One guy was running Linux with a tiling window manager - I think it was i3.
His setup did not play well with the projector.
A supervisor ended up lending him his laptop to present, so it was not the end of the world but still embarrassing, I imagine.

In between presentations I started a conversation, having dabbeled in window managers myself.
I asked him, what window manager he was running.
I also told him that I don't use window managers anymore, because you have to manage everything yourself.
He got kind of mad and told me he is way more productive with a window manager.

I fully agree with him, tiling behavior is excellent for programmers.
But I also told him you can get it on [KDE Plasma](https://kde.org/plasma-desktop/) with [Bismuth](https://github.com/Bismuth-Forge/bismuth).
It works great, I told him.
__Until it doesn't...__

## Desktop Environments Break Too

Two days ago I woke up feeling productive.
I went about my morning routine and then sat down ready to tackle my tasks for the day.
But when I opened the terminal, it wasn't big as it should with bismuth.
Tiling had stopped working.

I spent the next three hours installing and customizing [Awesome WM](https://awesomewm.org/) to get it to a usable state.
The picture above is the default configuration reproduced today on my laptop.
After that whole ordeal I was finally ready to work.

## Problems

As soon as I started, I reached to the oversized volume wheel on my keyboard to turn up the music.
It took a second until I realized that it doesn't work out of the box...
That's a big problem for me because I listen to music all the time.
Also, the play/pause button does not work.

I tried different plugins, but they didn't work right away.
So I wrote my own quick and dirty implementation for volume control.
First we have to add key bindings to some functions we will implement later.

```lua
local volume = require("volume")

// ...
globalkeys = awful.util.table_join(
    // ...
    awful.key({}, "XF86AudioRaiseVolume", function() volume.change_volume(1) end),
    awful.key({}, "XF86AudioLowerVolume", function() volume.change_volume(-1) end),
    awful.key({}, "XF86AudioMute", function() volume.toggle_volume() end),
    // ...
}
```

Of course, we have to include the model `volume`, we are about to create at the top of the file.
Then we create a new file in the same directory as the main config file `~/.config/awesome/rc.lua` called `volume.lua`.
It will define the functions we used in the keybindings above.

```lua
local awful = require("awful")

local volume = {}

local controller = 'pactl'
local sink = 1

function volume.change_volume(delta)
    local cmd = controller .. ' set-sink-volume ' .. sink .. ' '
    if delta > 0 then
        cmd = cmd .. '+'
    end
    cmd = cmd .. delta .. '%'
    awful.spawn(cmd)
end

return volume
```

This little script builds a command for when we change the volume using `pactl` since I am running [Pipewire](https://pipewire.org/).
We have to add a plus in front of the number `delta` in case it is positive for it to work correctly.

I can already hear you say "But clouds, where is the function for the mute button?"
Well I was too lazy to implement it yet, since I barely need it.
But getting the play/pause button to work would be nice.
Also, the sink ID is hard-coded right now, which I will have to change once I want to run it on my laptop.

Other smaller problems like `mpv` not covering the top bar cropped up here and there but were easy to fix.
It is annoying though.
Another problem, I didn't even think about before was Wi-Fi.
When recreating the screenshot at the top of the article, my laptop did not automagically connect to Wi-Fi.
These are things, we take for granted but are not a given when wandering off into window manager land.

### NVIDIA

Yesterday I was playing around with `mpv` and fixed the issue mentioned above.
I also had screen tearing.
But fortunately I already knew how to fix this.
See, I am running an NVIDIA graphics card.
__Big Mistake.__
These cards don't play nice with Linux.

[The fix](https://christitus.com/fix-screen-tearing-linux/) is straight forward and involves enabling "Force Full Composition Pipeline" in the NVIDIA settings.
Then you have to save the configuration to the X configuration file.
This didn't work since I needed root access to change the file and I only managed to open the settings without root access.
So I went ahead and copied the preview of the file manually into the X configuration file `/etc/X11/xorg.conf`.
Without backing up the original file.
__Big Mistake.__

I turned off my computer and went out biking with a friend.
When I came back and just wanted to kick back and relax, maybe watch some anime.
Instead, I was greeted with this:

{{< image src="/posts/awesome-wm-init/login.jpg" alt="TTY Login Screen" position="center" style="border-radius: 8px;" >}}

<p style='text-align: center;'> <b> Not Fun. </b> </p>

Reconfiguring X was a pain since I was not exactly sober.
But now I know how to do it in the future.
Hopefully, I won't need to do it and will remember to _back up_ the working configuration in the future.

<p style='text-align: right;'> Why am I doing this to myself? </p>

## Are Desktop Environments better?

The short answer is "yes".
The long answer is "it depends", as so often in computer engineering.
A window manager without a desktop environment is right for you, if you
- Want a better tiling experience
- Like customizing your desktop experience
- Don't mind spending hours on your computer reading documentation and playing with your configuration files
- Are not afraid of the terminal and the TTY

If you want something that just works, you should definitely go with a desktop environment like KDE Plasma or GNOME.

---

### Why Awesome?

I was first exposed to Awesome WM on [r/unixporn](https://www.reddit.com/r/unixporn), a community for ricing your Linux desktop experience.
The posts using this window manager were beautiful, but so were posts using other window managers.
I didn't think much about it and continued with my KDE Plasma setup.

Later a coworker at my last job mentioned he wanted to test out Awesome WM.
At that point I didn't know much about it.
I probably would have been a lot more enthusiastic about his choice if I knew the following information.

By complete chance I saw a [YouTube video](https://www.youtube.com/watch?v=L1SNxfx71eE) recently, explaining the difference between soft and hard forks.
There the creator mentioned, that Awesome WM is a hard fork of [dwm](https://dwm.suckless.org/).
Having used dwm myself for about a year, before switching distros and hopping on KDE Plasma I immeadiately made plans to use it as my next window manager, should I ever leave the comfort of a desktop environment.
