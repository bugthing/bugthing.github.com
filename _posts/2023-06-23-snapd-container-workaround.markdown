---
layout: default
title: Container work-around
---

I was unable to get systemd (via quadlet) to run the syncthing container via this file `HOME/.config/containers/systemd/syncthing-.container`

```
[Unit]
Description=Syncthing Container
After=network-online.target

[Container]
Image=registry.hub.docker.com/syncthing/syncthing:latest
PublishPort=8384:8384/tcp
Volume=/home/benjamin/container-volumes/syncthing:/var/syncthing:z
UserNS=keep-id

[Install]
WantedBy=multi-user.target default.target
```

when i did

```
systemctl --user daemon-reload
systemctl start syncthing
```

I could see via `journalctl -f` this error

```
Started syncthing.service - Syncthing - Open Source Continuous File Synchronization for .
Jun 23 10:50:36 terra (yncthing)[36058]: syncthing.service: Failed at step EXEC spawning /var/lib/snapd/snap/bin/syncthing: No such file or directory
```

so something odd was happening with snapd when running via systemd

My workaround for now is to create the container by hand

```
podman run -d --name=syncthing -v $HOME/container-volumes/syncthing:/var/syncthing:z -p 8384:8384/tcp --userns=keep-id syncthing/syncthing
podman stop syncthing
```

then create a systemd service to run the existing container by name:

```
mkdir -p ~/.config/systemd/user
podman generate systemd --name syncthing > ~/.config/systemd/user/syncthing.service
systemctl --user daemon-reload
systemctl enable --user --now syncthing.service
```

