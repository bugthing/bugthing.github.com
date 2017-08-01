---
layout: default
title: Arch broke and it was awesome!
---
I use Arch Linux running on an encrypted root filesystem (btrfs)
The other day I rebooted and it could not decrypt my file system .. I was faced with this:

![Broken Arch](/images/broke_arch.jpg)

I thought "no problems", I'll just reboot into last good boot snapshot.

I booted into my fail safe snapshot.. the problem remained!, I thought "oh poo!"

Next I booted of an arch usb, decryped my drive using `cryptsetup`, that worked, "phew!" at least I can see my data.

I then mount `/` and `/boot`, chrooted and ran `pacman -U /var/cache/pacman/pkg/systemd-233-7-x86_64.pkg.tar.xz` to go back to
the previous version of systemd. After a reboot I could enter my password and dcrypted my drive again. "YAY!"

I reported this problem on the arch forum to see if other had same issue, turns out they do and there is a fix on the way

[already reported issue](https://bbs.archlinux.org/viewtopic.php?id=228670)

I am now waiting for a newer version of systemd to turn up.. so for now run this to update:

   $ pacman -Syyuu --ignore systemd

Althou it was bad to break and scary, it was awesome as I was able to fix it and I got to talk on people on Arch forum.

