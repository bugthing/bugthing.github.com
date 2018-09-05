---
layout: default
title: Bluetooth + PulseAudio in my terminal life
---
I recently swapped from Arch Linux to SolusOS to give it a try... its really great distro
and good for a work and home machine being great for development and gaming.

The only problem I had was it seemed slower that Arch .. not sure why, perhaps ext4 vs btrfs?
.. anyway, I'm back on Arch and trying to resist installing big shiney desktop like gnome or kde

So sticking with i3 and trying to keep packages to a minmum I am using cli tools to
do what I would usually use a GUI tool for.

### Bluetooth

I would usually use `bluez-manager`, but now i do this

    $ bluetoothctl
    [bluetooth]# power on
    [bluetooth]# agent on
    [bluetooth]# default-agent
    [bluetooth]# connect 00:1D:43:6D:03:26

To pair with devices, do this:

    [bluetooth]# scan on
    [bluetooth]# pair 00:1D:43:6D:03:26

### Sound management

I would usually use `pavucontrol`, but now i do this

Volumes, etc.

    $ pulsemixer

List outputs

    $ pactl list short sinks

Set default output

    $ pacmd set-default-sink <OUTPUT>
    $ pacmd set-default-sink 1

List inputs

    $ pactl list short sink-inputs

Change an input to a different output

    $ pactl move-sink-input <INPUT> <OUTPUT>
    $ pactl move-sink-input 290 0

### File management

Tried midnight commander but that lack of automounting usb was too much
so I use `pcmanfm` and install some gvfs types

    $ pacman -S pcmanfm gvfs-gphoto2 gvfs-mtp gvfs-smb



