---
layout: default
title: Configuring distrobox to assemble an arch container
---
Currently I am using [Bluefin](https://projectbluefin.io/) for my desktop.
It works really well for ruby developement if you combine `linuxbrew` and `distrobox`.
I use the following `/etc/distrobox/distrobox.yml` configuration.

```ini
[arch]
image=ghcr.io/ublue-os/arch-distrobox:latest
nvidia=true
pull=true
volume=/home/linuxbrew:/home/linuxbrew
additional_packages="base-devel bash-completion chromium flatpak-xdg-utils git inetutils libyaml lsof man-db man-pages nss-mdns openssh postgresql-libs rsync wget curl zip"
start_now=true
init_hooks=ln -sf /usr/bin/distrobox-host-exec /usr/local/bin/docker;
init_hooks=ln -sf /usr/bin/distrobox-host-exec /usr/local/bin/flatpak;
init_hooks=ln -sf /usr/bin/distrobox-host-exec /usr/local/bin/podman;
init_hooks=ln -sf /usr/bin/distrobox-host-exec /usr/local/bin/xdg-open;
```

This means I can assemble an arch container for development by running `just assemble arch`
