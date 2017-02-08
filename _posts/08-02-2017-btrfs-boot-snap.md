---
layout: default
categories: blog
tags: linux cli btrfs bootctl
title: Take a btrfs snapshot on each successfull boot
---

To try and keep my system bootable no matter what happens I use systemd to run a script that takes a btrfs snapshot and configures bootctl to add that snapshot into the boot menu.

To do this first, write the following script here **/usr/local/bin/snapshot_current_system_state.sh** (and make it executable)

    #!/bin/sh

    # take no snapshots when booted into a snapshot
    if [ -e '/SNAPSHOT-TIMESTAMP' ]
    then
      exit 0
    fi

    echo "Deleting old snapshot.."
    /usr/bin/btrfs subvolume delete /run/btrfs-root/__state_at_last_successful_boot

    echo "Snap shotting root.."
    /usr/bin/btrfs subvolume snapshot /run/btrfs-root/__current/ROOT /run/btrfs-root/__state_at_last_successful_boot

    timestamp="$(/usr/bin/date +%d.%m.%Y-%H:%M:%S)"
    echo "Write issue file in new snapshot.."
    /usr/bin/echo -e "Arch Linux --- state at last successful boot (nonpersistent) [${timestamp}]\n" > /run/btrfs-root/__state_at_last_successful_boot/etc/issue

    echo "Write STAMP file in new snapshot.."
    /usr/bin/echo "${timestamp}" > "/run/btrfs-root/__state_at_last_successful_boot/SNAPSHOT-TIMESTAMP"

    echo "Replace fstab mount options, so we load the snapshot.."
    /usr/bin/sed --posix --regexp-extended "s/subvol=__current\/ROOT/subvol=__state_at_last_successful_boot/g" --in-place "/run/btrfs-root/__state_at_last_successful_boot/etc/fstab"

    /usr/bin/sync

Next, write the following systemd file **/etc/systemd/system/multi-user.target.wants/snapshot_current_system_state_upon_boot.service** (and enable it)

    [Unit]
    Description=Takes a snapshot of _current/ROOT after multi-user.target has been reached.
    After=multi-user.target

    [Service]
    Type=oneshot
    ExecStart=/bin/sh /usr/local/bin/snapshot_current_system_state.sh

    [Install]
    WantedBy=multi-user.target

This is NOT generic and pretty specific to my setup, so please read and grok the script and alter accordingly.
