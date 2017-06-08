---
layout: default
categories: blog
tags: linux games
title: Steam on Arch Linux
---

I use Steam on my arch linux machine.

It works well nice with BumbleBee doing the video card switch on my XPS15

As i run Arch linux and it seems steam is built to run on Ubuntu it sometimes breaks... mainly down to the use of incorrects libs

To fix this I run steam like so:

    $ env LD_PRELOAD='/usr/$LIB/libstdc++.so.6 /usr/$LIB/libgcc_s.so.1 /usr/$LIB/libxcb.so.1' /usr/bin/steam -bigpicture %U

There was also a problem with using the wrong encryption lib.. fixed like so:

    $ find ~/.steam/root/ -name "libgpg-error.so*" -print -delete

