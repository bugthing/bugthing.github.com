---
layout: default
title: md raid to zfs
---
I was very happy with my CoreOS home server, it was stable and pretty much trouble free.
.. but then I noticed [this](https://github.com/bsherman/ucore-hci) which seemed like a
great way to get to play with ZFS, without having to mess about with kernel mods.

So I went ahead a did:

      sudo bash
      ostree admin pin 0
      systemctl stop zincati
      rpm-ostree rebase ostree-unverified-registry:ghcr.io/bsherman/ucore-hci:stable
      systemctl reboot


Amazinginly it worked perfectly and I was now running CoreOS with some nice extras, most
notable for me, ZFS and cockpit.

(I did run into some trouble with using `mdadm` to manage my existing raid1 array, but
I contacted the ublue team and they had a fix within 30 mins.)

My next challange was to move my raid1 array to ZFS. Seeing as I only have 2 large drives
I have to remove 1 disk, convert to ZFS, copy data over then remove/add the 2nd drive.

To remove the disk from my raid1 array:

      mdadm --detail /dev/md127 # check raid array info

      mdadm --fail /dev/md127 /dev/sdd1 # fail 1 of the devices

      mdadm --detail /dev/md127 # check the drive has been failed

      mdadm --remove /dev/md127 /dev/sdd1 # remove the drive from the array

      mdadm --zero-superblock /dev/sdd1 # remove superblock so its seen as a regular drive

To convert the removed device to ZFS and copy data to it

      mkdir /var/lib/tmpzfs  # create tmp mount point

      zpool create zfs1 -o ashift=12 -m /var/lib/tmpzfs /dev/sdd

      zfs set compression=lz4 zfs1

      rsync -ah --progress /var/lib/backup /var/lib/tmpzfs

Check the data in /var/lib/tmpzfs has copied over as you'd expect.

      du -h /var/lib/tmpzfs
      find /var/lib/tmpzfs

Remove the 2nd device from the raid1 array

      mdadm --stop /dev/md127
      mdadm --zero-superblock /dev/sda1

Now attach the 2nd device to the vdev (this will create a mirrored vdev)

      zpool attach zfs1 /dev/sdd /dev/sda

ZFS will now resilver the disk, you can check the status

      zpool status zfs1

Change the mount point to where the raid2 array was mounted

      zfs set mountpoint=/var/lib/backup zfs1

