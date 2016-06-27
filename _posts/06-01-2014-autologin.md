---
layout: default
categories: blog
tags: linux cli
title: Turn off login for a linux machine
---

Sometimes I want a linux machine to bootup into a command prompt (no user and password required).

Clearly this is a massive security problem but for VM's and home exsperimental network it can be handy.

So open the following file:

    /etc/inittab

Find the line simlar to this:

    1:2345:respawn:/sbin/getty 115200 tty1

Comment it out and add this in its place, replacing USERNAME with the username of the account you want to be automatically logged in:

    1:2345:respawn:/bin/login -f USERNAME tty1 </dev/tty1 >/dev/tty1 2>&1


