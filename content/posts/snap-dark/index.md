---
title: "reSnap Maintenance"
date: 2023-07-29T22:02:11+02:00
draft: false
toc: false
images:
tags: 
    - reSnap
    - reMarkable
    - OSS
---

I have this script called [reSnap](https://github.com/cloudsftp/reSnap).
It is a tool to take screenshots of your [reMarkable](https://remarkable.com/) tablet over SSH.
This way you can use it over Wi-Fi.
The main inspiration and source of most code and ideas was [reStream](https://github.com/rien/reStream), a script to live stream what is being displayed on the tablet over SSH.

The script makes use of [lz4](https://github.com/lz4/lz4), a fast compression algorithm.
To install this on the reMarkable tablet, one would use [Toltec](https://toltec-dev.org/), a package manager for the reMarkable tablet.
Installing this disables the automatic updates on your tablet.
Which is a good thing, because just a week ago someone brought to my attention, that using Toltec with never versions of the reMarkable software might brick your tablet.
[Here is the issue](https://github.com/cloudsftp/reSnap/issues/12).
Basically new users can't use the script, because they have no supported way of installing lz4 on the tablet.

I enabled taking screenshots without `lz4` installed, which takes a lot longer.
His issue was fixed, but he also mentioned the screenshots being very dark.
I didn't have this issue and closed the original GitHub issue without thinking much of it.

{{< image src="/posts/snap-dark/img/no-correction.png" alt="Uncorrected Image" position="center" style="border-radius: 8px;" >}}

This Friday I was trying to be productive once again.
I often use this script when I do calculations and I need the results of the last page.
I do a screenshot and display it on my PC and then go to the next page on my tablet.
Unfortunately, the image was very dark.

<p style='text-align: center;'> Well... <b> shit </b> </p>

The image above illustrates the darkness, but it is of course not the screenshot where I ran into this problem.

## Fixing the problem

In the original issue, the author kindly provided me with a resource where someone else [corrected the image brightness in reStream](https://github.com/rien/reStream/pull/92).
It involves adding a filter to `ffmpeg`.
The filter was `curves=all='0/0 0.07/1 1/1'`.
At this point, I basically don't know anything about `ffmpeg` filters, so I just copied the filter into my script.
The image below is the result of this attempt.

{{< image src="/posts/snap-dark/img/first-correction.png" alt="First Correction" position="center" style="border-radius: 8px;" >}}

Ok this is too bright.
After a little bit of trial and error, I got tired of it.
Especially, since the screenshots now take a lot longer without `lz4`.
So I turned to the documentation and figured out, that the parameters in the curves filter are points of a function that translates the color.
Ok so I need to figure out that curve.

How can I do that?
I figured maybe I can use GIMP.
So I fired it up and loaded the dark screenshot.
Searched for a color correction option, and viola, there is an option called 'Curves'.
This process of trial and error was a lot easier and the final curve was a very steep curve.

{{< image src="/posts/snap-dark/img/gimp-correction.png" alt="GIMP Correction" position="center" style="border-radius: 8px;" >}}

Now I just needed to translate the points from the domain [0, 255] to [0, 1] and adjust it slightly to my liking.
I finally settled on `curves=all=0.045/0 0.06/1`.

## reMarkable 1

I was ready to merge, but then I remembered that for the reMarkable 1, the script uses a different technique to get the pixels on the screen.
On version 1 it uses the following command.

```bash
dd if=/dev/fb0 count=1 bs=$window_bytes 2>/dev/null
```

While on version 2 it uses this command.

```bash
dd if=/proc/$pid/mem bs=$page_size skip=$window_start_blocks count=$window_length_blocks 2>/dev/null |
    tail -c+$window_offset |
    cut -b -$window_bytes
```

I still want to support the reMarkable 1, so I don't want to ruin the image quality for the users with this tablet.
But I don't have the reMarkable 1 anymore.
I gave it away to a friend that studies law, so I couldn't ask him to just test my script.
He was willing to come over and let me test the script on his hardware.
I had some issues, but finally figured, that the reMarkable 1 does not need color correction.

# Release

Finally, it was time to release my fix.
I turned it on only for the reMarkable 2 and also added an option `--og-color` to turn it off.
Additionally, I added an environment variable, where users can configure the default behavior, `RESNAP_COLOR_CORRECTION`.
Lastly I added a statically linked `lz4` binary for the tablet and instructions to install it.
This way, users can install `lz4` manually without the need for Toltec.

