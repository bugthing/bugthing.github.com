---
layout: default
published: true
categories: blog
tags: linux desktop openbox
title: Network Manager permissions (nm-applet)
---

One of the most popular desktop network managers is called (in the Debian repos)
"network-manager" and a way to control this is to use "nw-applet"

When building my desktop I discovered, if you install it and "xdg" it will
get started automatically (see /etc/xdg/autostart/) .. so if this is the case
dont bother starting it manually.

I also discovered some permission issues with it, when it was reporting:

    "(32) Insufficient privileges"

or

    "(32) Not authorized to control networking"

This can be fixed by using policy kit and creating the following file:  
  
    /etc/polkit-1/localauthority/50-local.d/org.freedesktop.NetworkManager.pkla

Then putting the following content within:

    [nm-applet]
    Identity=unix-group:network
    Action=org.freedesktop.NetworkManager.*
    ResultAny=yes
    ResultInactive=no
    ResultActive=yes

