---
layout: default
categories: blog
tags: linux
title: Install Arch on a usb stick
---
Some steps to follow in order to install Arch linux on a USB stick (from an existing Arch Linux)

First install the scripts required to install Arch.

    $ sudo pacman -S arch-install-scripts

Insert the USB stick and find out which device is has been assigned.

    $ lsblk

From here we will assume `/dev/sda`.
Partition the USB as required. Here we will have 1 boot partition and 1 root partition.

    $ gdisk /dev/sda
    o<confirm>
    n<enter><enter><enter>+512M<enter>EF00<enter>
    n<enter><enter><enter><enter><enter>
    p<enter>
    w<enter><confirm>

Now lets format the partitions to create the file systems. We are formating using the
special EFI format (fat32) and EXT4.

    $ mkfs.fat -F32 -nBOOT /dev/sda1
    $ mkfs.ext4 -L EXT4 /dev/sda2

Now lets mount the root parition

    $ mount /dev/sda2 /mnt

Also create a dir to mount the boot partition, then mount it

    $ mkdir /mnt/boot
    $ mount /dev/sda1 /mnt/boot

The following bootstraps an Arch installation in a given director. This basically
creates all directories and files required to run Arch Linux.

    $ pacstrap /mnt base

As part of the linux boot process the `/etc/fstab` file is read to discover which
devices should be mounted, where and which options. This generates the /etc/fstab

    $ genfstab -U /mnt >> /mnt/etc/fstab

We now have enough in `/mnt` to chroot into it and it act like a freash install.

    $ arch-chroot /mnt

You can now see the fstab we generated in `/etc/`, open it and clean it up if required.

    $ vi /etc/fstab
    <clean up, swap, etc.>

Now do some jobs to configure the machine.
Set the timezone

    $ ln -sf /usr/share/zoneinfo/Europe/London /etc/localtime

Set the locale

    $ vi /etc/locale.gen
    <uncomment your locale>
    $ locale-gen

Set keymap

    $ echo KEYMAP=uk > /etc/vconsole.conf

Set hostname

    $ echo archusb > /etc/hostname
    $ vi /etc/hosts
    127.0.0.1       localhost
    ::1             localhost
    127.0.1.1       archusb.localdomain     archusb

Nearly done, we just want some extra packages so lets add them now

    $ pacman -S                   \
        dialog                    \
        wpa_supplicant            \
        iw                        \
        base-devel                \
        intel-ucode               \
        arch-install-scripts      \
        archlinux-menus           \
        git                       \
        sudo                      \
        less                      \
        ufw                       \
        openssh                   \
        docker                    \
        flatpak                   \
        file                      \
        imagemagick               \
        cups                      \
        dmenu                     \
        dunst                     \
        feh                       \
        fzf                       \
        ripgrep                   \
        gpaste                    \
        gvfs-mtp                  \
        hdparm                    \
        hplip                     \
        i3                        \
        gnome-icon-theme          \
        blueman                   \
        bluez                     \
        bluez-utils               \
        libmariadbclient          \
        libmtp                    \
        libyaml                   \
        nautilus                  \
        network-manager-applet    \
        networkmanager            \
        pavucontrol               \
        pulseaudio                \
        pulseaudio-bluetooth      \
        python-xdg                \
        ranger                    \
        redshift                  \
        rxvt-unicode              \
        ttf-bitstream-vera        \
        ttf-dejavu                \
        xorg-server               \
        xorg-xfontsel             \
        xorg-xinit                \
        xorg-xrandr               \
        scrot                     \
        stow                      \
        mpd                       \
        pass                      \
        vim                       \
        tmux                      \
        chromium                  \
        firefox                   \
        youtube-dl

Configure mkinitcpio which creates the initial ramdisk environment

    $ vi /etc/mkinitcpio.conf
    MODULES="vfat aes_x86_64 crc32c-intel i915 ata_generic"

    $ mkinitcpio -p linux

Install and configure the boot loader

    $ bootctl --path=/boot install
    $ cp /usr/share/systemd/bootctl/arch.conf /boot/loader/entries/arch.conf
    $ ls -sl /dev/disk/by-partuuid/
    $ vi /boot/loader/entries/arch.conf
    initrd /intel-ucode.img
    options root=PARTUUID=<blah blah> rootfstype=ext4 add_efi_memmap

Enable any services you want the system starts

    $ systemctl enable docker
    $ systemctl enable sshd

Setup password and your own user (here we create user *pleb*)

    $ passwd
    $ useradd -m -g users -G network,power,docker,storage,audio,wheel -s bash pleb
    $ passwd pleb
    $ su - pleb

Configure X

    $ cp /etc/X11/xinit/xinitrc ~/.xinitrc

    $ vim ~/.xinitrc
    # load Xresources
    [[ -f ~/.Xresources ]] && xrdb -merge -I$HOME ~/.Xresources
    # start pulseaudio
    exec pulseaudio --start &
    # set DISPLAY (mainly for dunst)
    systemctl --user import-environment DISPLAY
    # start ssh-agent
    eval $(ssh-agent)
    # desktop
    exec i3

Now you are ready to boot using your newly created Arch Linux USB stick. Lets unmount
sync and reboot:

    $ exit
    $ exit
    $ umount /mnt/boot
    $ umount /mnt
    $ sync
    $ reboot

