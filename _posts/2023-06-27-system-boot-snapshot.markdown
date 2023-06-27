---
layout: default
title: SystemD Boot Snapshotting
---

I run my root file system on btrfs and when I update I take a snapshot before anything is changed.
Meaning if something breaks, I can reboot into previous system.

As my /boot partition runs on a fat filesystem I can not include the boot files. To cope with this
I copy the boot files to an alternative location.

To do this I use the following `update` script

```
#!/bin/bash

set -e

snapshot=/run/btrfs-root/@before_last_update
echo "Deleting old snapshot: ${snapshot}"
sudo btrfs subvolume delete ${snapshot} || echo "Did not delete old snapshot"

rootDir=/run/btrfs-root/@
echo "Snap shotting $rootDir"
sudo btrfs subvolume snapshot $rootDir $snapshot

echo "Copying bootfiles"
esp=/boot
cp -f ${esp}/vmlinuz-linux ${esp}/before_last_update/
cp -f ${esp}/intel-ucode.img ${esp}/before_last_update/
cp -f ${esp}/initramfs-linux.img ${esp}/before_last_update/
cp -f ${esp}/initramfs-linux-fallback.img ${esp}/before_last_update/

echo "Running pacman update"
sudo pacman -Syyuu

```

I also have a systemd-boot loader entry like so

```
title Arch Linux before the last update
linux /before_last_update/vmlinuz-linux
initrd /before_last_update/intel-ucode.img
initrd /before_last_update/initramfs-linux.img
options rd.luks.name=uuid-of-the-root-partition=cryptroot root=/dev/mapper/cryptroot rootflags=subvol=@before_last_update,compress=lzo
```
