---
layout: default
categories: blog
tags: ssl http web linux
title: HTTPS and SSL .. test it out
---

To test if a machine can get to a port and get some data I often use 'telnet' 
or 'nc'  but what if the thing you are connecting to uses SSL?

So normal testing of a http site you could use

    telnet www.google.co.uk 80

for an HTTPS site, the above will not work, by openssl brings a solution:

    openssl s_client -connect www.google.co.uk:443

