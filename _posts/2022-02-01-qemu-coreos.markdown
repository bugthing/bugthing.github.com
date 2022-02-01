---
layout: default
title: qemu-coreos
---
I am having trouble with a bearmetal install of CoreOS with raid0 and raid1 arrays. I decided to try
out the config in a virtual machine.

So first I created 3 harddisk drives

      qemu-img create temp/coreos_disk1.img 10g
      qemu-img create temp/coreos_disk2.img 10g
      qemu-img create temp/coreos_disk3.img 10g

This started the virtual machine with the drives attached and .iso file in the cdrom, like so:

      qemu-system-x86_64 -m 512  -boot d -cdrom Downloads/fedora-coreos-35.20220103.3.0-live.x86_64.iso  \
        -hda temp/coreos_disk1.img -hdb temp/coreos_disk2.img \
        -hdd temp/coreos_disk3.img \
        -enable-kvm

The VM booted off the iso and I could see from `ls -al /dev/disks/by-id` the ID of disks.
Knowing the ID of disks I was able to adjust a [butane config](https://github.com/bugthing/coreos-home-server/blob/main/home-server.yml), make available via local http server, and install with:

      coreos-install install /dev/sda --ignition-url http://192.168.1.111:8000/home-server.ign --insecure-ignition

Once installed a ran `poweroff` and started virtual machine again.. this time booting from hdd and doing a local port forward for ssh access

     qemu-system-x86_64 -m 512 -boot c \
       -hda temp/coreos_disk1.img \
       -hdb temp/coreos_disk2.img \
       -hdd temp/coreos_disk3.img \
       -enable-kvm \
       -device e1000,netdev=net0 -netdev user,id=net0,hostfwd=tcp::5555-:22

