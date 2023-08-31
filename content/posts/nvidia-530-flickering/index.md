---
title: "The Problem with NVIDIA"
date: 2023-08-31T20:10:07+02:00
draft: true
toc: false
images:
tags:
  - NVIDIA
---

I wanted to do this post for a while now, but I have been caught up in my thesis.
Today I handed in the digital version and am now waiting to hand in the physical copy.

## Black Flickering

So, about a month ago, my screen started flickering.
It was only the top third of the screen, and the flickers came in short bursts.
Nonetheless, it was annoying.

See, I am running an NVIDIA graphics card.
__Big Mistake.__ </br>
So I already knew what the cause of the problem was.
Well, not exactly, but I knew the NVIDIA driver update was to blame.
I searched around the internet a little and finally found a [forum post](https://forums.developer.nvidia.com/t/very-short-black-flickering-flashes-with-nvidia-2080ti-since-nvidia-530-41-03-driver/248354/13).
It seemed like an update that I just received --- 530 --- caused problems for many other NVIDIA graphics cards...

The fix was to add the parameter `nvidia_drm.modeset=1` to your bootloader.
I am running openSuse Tumbleweed, so I added the parameter to the variable `GRUB_CMDLINE_LINUX_DEFAULT` in `/etc/default/grub`.
After that, you need to rebuild the bootloader, so I ran `sudo grub2-mkconfig -o /boot/grub2/grub.cfg`.

## Great --- Problem Solved

Not.
It reduced the issue by about 50% tho.

I don't know whether I did something wrong or this fix does not work for my model.
But after a few days or weeks, I thought of another thing that might be the problem.
GPU sack.
- Add Hypothesis
So I got my PC from under the table and looked inside it.

{{< image src="/posts/nvidia-530-flickering/card-before-wide.jpg" alt="Bent GPU" position="center" style="border-radius: 8px;" >}}

Yeah, it's definitely bending.
To test my hypothesis, I put some of my favorite books under the graphics card for support.

{{< image src="/posts/nvidia-530-flickering/card-temp-wide.jpg" alt="Books GPU" position="center" style="border-radius: 8px;" >}}

You can clearly see that now it is not bending anymore.

- A few test runs
- great results - almost no flickering
- not a good solution
- someone told me not to use my good mangas
- got something from Amazon


{{< image src="/posts/nvidia-530-flickering/card-final-wide.jpg" alt="Final GPU" position="center" style="border-radius: 8px;" >}}


#### I need to get an AMD graphics card
<p style='text-align: center;'> I need to get an AMD graphics card </p>
