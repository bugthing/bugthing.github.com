---
layout: default
title: SystemD Path and Service
---

When you need to do something if a file has changed, you can use a systemd `.path` file
to execute a `.service` of the same name.

`/etc/systemd/system/efistub-update.path`

```
[Unit]
Description=Trigger copy of boot files to EFI partition

[Path]
PathChanged=/boot/initramfs-linux-fallback.img

[Install]
WantedBy=multi-user.target
WantedBy=system-update.target
```

`/etc/systemd/system/efistub-update.service`

```
[Unit]
Description=Copy boot files to EFI partition

[Service]
Type=oneshot
Environment=esp=/efi
ExecStart=/usr/bin/cp -f /boot/vmlinuz-linux ${esp}/before_last_update/
ExecStart=/usr/bin/cp -f /boot/intel-ucode.img ${esp}/before_last_update/
ExecStart=/usr/bin/cp -f /boot/initramfs-linux.img ${esp}/before_last_update/
ExecStart=/usr/bin/cp -f /boot/initramfs-linux-fallback.img ${esp}/before_last_update/
```
