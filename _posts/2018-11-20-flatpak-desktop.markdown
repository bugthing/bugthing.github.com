---
layout: default
title: flatpak-desktop
---

In the never ending quest to have the most up to the minute desktop features yet a supxera stable system
I am currently using an Arch base ontop of a BTRFS filesystem then utilising contained packages like
snaps and flatpaks to get none essential software. Currently favouring flatpaks for desktop stuff.

Flatpak puts executables here:

    /var/lib/flatpak/exports/bin

so add it to your $PATH

Here is how I get my gnome stuff:

1. Add the flatpak source

    $ sudo flatpak remote-add --from gnome-apps https://sdk.gnome.org/gnome-apps.flatpakrepo

2. See what available

    $ flatpak remote-ls gnome-apps

3. Install alittle something

    $ sudo flatpak install gnome-apps org.gnome.Nautilus


