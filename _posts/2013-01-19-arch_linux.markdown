---
layout: default
categories: blog
tags: linux arch
title: Arch Linux
---

I have been a full Debian advocate and user for years, I still am.

The only thing that frustrates me is that I often like to try out new software and Debian is everything.. except the newest software.

I try to deal with this by using abit of screwed up mix of software from repositories stable, testing and experimental. As I dont really know that much about the workings of linux, this often ends up with me screwing my system and being unable to get it going.

This lead me to trying out Arch .. it is a rolling release of cutting edge software and so far I am really enjoying it.

I installed it on my Thinkpad x201 and it has been pretty much trouble free.. I used [these instructions](https://wiki.archlinux.org/index.php/Lenovo_ThinkPad_X201) to tweak it after the install.

I did run into a problem where my GnomeShell kept crashing, which I found to be something to with the i915 intel video driver (by looking in the /var/log/Xorg.1.log file).. but after a quick google, it turned out I had to load the driver early, but putting

    options i915 modeset=1

into this file: /etc/modprobe.d/modprobe.conf

The referencing it within: /etc/mkinitcpio.conf, like so:

    FILES="/etc/modprobe.d/modprobe.conf"

Well, I think it was that that fixed my problem, but I also installed, xf86-video-fbdev and xf86-video-vesa which "might" have helped??

I am now happily using Arch and it seems very fast and stable.
