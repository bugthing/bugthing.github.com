---
layout: default
categories: blog
tags: linux arch gnome
title: Changing the gdm background (gnome-shell)
---

I have been using Arch as my desktop for a while now, and I really like it...
mainly as (so far) it seems stable and very very up to date, so I get all the
newest toys, like gnome 3.8

Each time gnome is updated my gdm background is reconfigured, to put it back I
do the following:

Open gnome-shell.css file:

  sudo vi /usr/share/gnome-shell/theme/gnome-shell.css

Change the following class definition to be:

  #lockDialogGroup {
        background-image: url("skate_login.png");
        background-repeat: repeat;
        background-size: cover;
  }

NOTE: you have to copy 'skate_login.png' to '/usr/share/gnome-shell/theme/'
