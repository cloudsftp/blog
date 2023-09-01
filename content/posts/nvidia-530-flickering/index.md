---
title: "The Problem with NVIDIA"
date: 2023-08-31T20:10:07+02:00
draft: false
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

<div style='text-align: center; font-size: 20pt'>
    <strong> Not. </strong>
</div>
It reduced the issue by about 50% tho.

I don't know whether I did something wrong or this fix does not work for my model.
But after a few days or weeks, I thought of another thing that might be the problem.
GPU sag.
Recently I saw a [YouTube video](https://www.youtube.com/watch?v=JFgsL5NFn_Y) by the hardware repair god Louis Rossman about GPU sag killing graphics cards.
This reminded me of another video I saw ages ago --- I think by [Mutahar](https://www.youtube.com/@SomeOrdinaryGamers) --- about GPU sag causing black flickering.
So I got my PC from under the table and looked inside it.

{{< image src="/posts/nvidia-530-flickering/card-before-wide.jpg" alt="Bent GPU" position="center" style="border-radius: 8px;" >}}

Yeah, it's definitely bending.

So my new hypothesis is that the update revealed a hardware problem that my graphics card was suffering from for a longer time.
Another piece of evidence is that the bug seems to be connected to power.
Somehow I came across this multiple times during my initial research into the bug.
[This forum post](https://forums.developer.nvidia.com/t/black-flickering-on-530-41-03-fedora-37/254211/6) is one example.

To test my hypothesis, I put some of my favorite books under the graphics card for support.

{{< image src="/posts/nvidia-530-flickering/card-temp-wide.jpg" alt="Books GPU" position="center" style="border-radius: 8px;" >}}

You can clearly see that now it is not bending anymore.
And the flickering got a lot better --- but it's still not completely gone.
But this is not a sustainable solution.
So I got [something from Amazon](https://www.amazon.de/-/en/dp/B0BY4NXNF9?psc=1&ref=ppx_yo2ov_dt_b_product_details).

{{< image src="/posts/nvidia-530-flickering/card-final-wide.jpg" alt="Final GPU" position="center" style="border-radius: 8px;" >}}

As you can see, this is a much better solution and the card is even more straight.
Now the flickering is almost completely gone.
I have been sitting here for 20 minutes without a single flicker.
Sometimes it flickers multiple times per minute tho.

The moral of the story is not to use a NVIDIA fraphics card in your Linux setup.
In the wise words of Linus Torvalds: [NVIDIA, fuck you!](https://www.youtube.com/watch?v=iYWzMvlj2RQ)

<div style='text-align: center; font-size: 20pt'>
    <strong> I need to get an AMD graphics card </strong>
</div>
