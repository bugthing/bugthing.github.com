---
layout: default
categories: blog
tags: linux gnome libre office
title: LibreOffice in gnome wayland scale issue
---

I loaded Libre Office in Gnome shell using the Wayland display server .. it was so wrong it was usable

until I checked here:

    https://bugs.documentfoundation.org/show_bug.cgi?id=99508

and found that I need to set the SAL_USE_VCLPLUGIN env var

basically, just run:

    $ SAL_USE_VCLPLUGIN=gtk libreoffice

