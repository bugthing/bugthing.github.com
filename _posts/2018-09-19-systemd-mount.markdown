---
layout: default
title: Using systemd to mount my USB drives
---

I setup my really old tower pc as a server for my house. I cant sell it (coz its shit) and hate throwing stuff away.

I installed Arch Linux and set about configuring the mounting of some usb drives. For this I went the systemd route.

Setting up mounts seems pretty easy.. but I was faces this error:

    Where= setting doesn't match unit name. Refusing.

Turns out you have to name the units after the path where you are going to mount (not sure why)

so I created the unit like so:

    $ sudo vim /etc/systemd/system/`systemd-escape -p --suffix=mount "/run/grey-usb-hdd"`

add the text like so:

    [Unit]
    Description=Mount USB harddrive (grey)

    [Mount]
    What=/dev/disk/by-label/USB-HDD
    Where=/run/grey-usb-hdd
    Type=ntfs
    Options=defaults

    [Install]
    WantedBy=multi-user.target

the just ran

    $ sudo systemctl enable 'run-grey\\x2dusb\\x2dhdd.mount'
    $ sudo systemctl start 'run-grey\\x2dusb\\x2dhdd.mount'
