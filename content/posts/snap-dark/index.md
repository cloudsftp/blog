---
title: "reSnap Maintenance"
date: 2023-07-29T11:02:11+02:00
draft: false
toc: false
images:
tags: 
    - reSnap
    - reMarkable
    - OSS
---

- First encounter, I did not have the problem
- One update later I had the problem
- Figuring out filter: load pic into GIMP and play around w/ curves

## reMarkable 1

- Still want to support version 1
- Inquired friend which version it runs
- Still not sure, since it gets the data differently

```bash
head_fb0="dd if=/dev/fb0 count=1 bs=$window_bytes 2>/dev/null"
```

vs.

```bash
head_fb0="dd if=/proc/$pid/mem bs=$page_size skip=$window_start_blocks count=$window_length_blocks 2>/dev/null |
    tail -c+$window_offset |
    cut -b -$window_bytes"
```

- Friend comes over

