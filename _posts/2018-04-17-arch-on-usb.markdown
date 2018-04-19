---
layout: default
categories: blog
tags: linux
title: Install Arch on a usb stick
---
Some steps to follow in order to install Arch Linux on a USB stick (from an existing Arch Linux)

First, install the scripts required to install Arch.

    $ sudo pacman -S arch-install-scripts

Insert the USB stick and find out which device is has been assigned.

    $ lsblk

From here we will assume `/dev/sda`.
Partition the USB stick as required. Here we will have 1 boot partition and 1 root partition.

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

Also create a dir to mount the boot partition in, then mount it

    $ mkdir /mnt/boot
    $ mount /dev/sda1 /mnt/boot

The following bootstraps an Arch installation in a given directory. This basically
creates all directories and files required to run Arch Linux.

    $ pacstrap /mnt base

As part of the linux boot process the `/etc/fstab` file is read to discover which
devices should be mounted, where and with which options. This generates the /etc/fstab

    $ genfstab -U /mnt >> /mnt/etc/fstab

We now have enough in `/mnt` to chroot into it and it act like a freash install.

    $ arch-chroot /mnt

You can now see the fstab we generated in `/etc/`, open it and clean it up if required.

    $ vi /etc/fstab
    <clean up, swap, etc.>

Now do some jobs to configure the machine.

Set the timezone:

    $ ln -sf /usr/share/zoneinfo/Europe/London /etc/localtime

Set the locale:

    $ vi /etc/locale.gen
    <uncomment your locale>
    $ locale-gen

Set keymap:

    $ echo KEYMAP=uk > /etc/vconsole.conf

Set hostname:

    $ echo archusb > /etc/hostname
    $ vi /etc/hosts
    127.0.0.1       localhost
    ::1             localhost
    127.0.1.1       archusb.localdomain     archusb

Nearly done, we just want some extra packages so lets add them now

    $ pacman -S        \
        dialog         \
        wpa_supplicant \
        iw             \
        sudo           \
        intel-ucode

Set a password for the root user:

    $ passwd

Configure mkinitcpio which creates the initial ramdisk environment:

    $ vi /etc/mkinitcpio.conf
    MODULES="vfat aes_x86_64 crc32c-intel i915 ata_generic"

    $ mkinitcpio -p linux

Install and configure the boot loader:

    $ bootctl --path=/boot install
    $ cp /usr/share/systemd/bootctl/arch.conf /boot/loader/entries/arch.conf
    $ ls -sl /dev/disk/by-partuuid/
    $ vi /boot/loader/entries/arch.conf
    initrd /intel-ucode.img
    options root=PARTUUID=<blah blah> rootfstype=ext4 add_efi_memmap

Now you are ready to boot using your newly created Arch Linux USB stick. Lets unmount,
sync and reboot:

    $ exit
    $ exit
    $ umount /mnt/boot
    $ umount /mnt
    $ sync
    $ reboot

Now you can boot into a very basic Arch Linux, you can build it exactly how you want it.
You can build any system you prefer, server, embedded, desktop, anything.
Here are some packages you might want in order to create a desktop machine for development
making use of a very lightweight desktop (i3):

    $ sudo pacman -S              \
        arch-install-scripts      \
        i3                        \
        gnome-icon-theme          \
        archlinux-menus           \
        gvfs-mtp                  \
        hdparm                    \
        gpaste                    \
        dmenu                     \
        dunst                     \
        feh                       \
        libmtp                    \
        libyaml                   \
        network-manager-applet    \
        networkmanager            \
        pavucontrol               \
        pulseaudio                \
        pulseaudio-bluetooth      \
        hplip                     \
        python-xdg                \
        ttf-bitstream-vera        \
        ttf-dejavu                \
        xorg-server               \
        xorg-xfontsel             \
        xorg-xinit                \
        xorg-xrandr               \
        rxvt-unicode              \
        blueman                   \
        bluez                     \
        bluez-utils               \
        nautilus                  \
        redshift                  \
        scrot                     \
        stow                      \
        mpd                       \
        ncmpcpp                   \
        pass                      \
        git                       \
        less                      \
        ufw                       \
        openssh                   \
        docker                    \
        flatpak                   \
        file                      \
        imagemagick               \
        cups                      \
        fzf                       \
        ctags                     \
        ripgrep                   \
        libmariadbclient          \
        vim                       \
        tmux                      \
        firefox

Enable the services you want so they start when the system starts:

    $ systemctl enable docker
    $ systemctl enable sshd

You'll want to add a user to login as: (here we create user *developer*):

    $ useradd -m -g users -G network,power,docker,storage,audio,wheel -s bash developer
    $ passwd developer

Logout and back in as your new user:

    $ exit
    username: developer
    password: computers

Install asdf to install and switch language versions (see: https://github.com/asdf-vm/asdf):

    $ git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.4.3
    $ echo -e '\n. $HOME/.asdf/asdf.sh' >> ~/.bashrc
    $ echo -e '\n. $HOME/.asdf/completions/asdf.bash' >> ~/.bashrc
    $ source ~/.bashrc
    $ asdf plugin-add ruby https://github.com/asdf-vm/asdf-ruby.git
    $ asdf plugin-add nodejs https://github.com/asdf-vm/asdf-nodejs.git
    $ asdf plugin-add elixir https://github.com/asdf-vm/asdf-elixir.git
    $ asdf plugin-add crystal https://github.com/marciogm/asdf-crystal.git
    $ asdf plugin-add java https://github.com/skotchpine/asdf-java
    $ asdf plugin-add python https://github.com/tuvistavie/asdf-python.git
    $ asdf plugin-add golang https://github.com/kennyp/asdf-golang.git
    $ asdf plugin-add elm https://github.com/vic/asdf-elm.git
    $ asdf plugin-add haskell https://github.com/vic/asdf-haskell.git

Configure your desktop:

    $ cp /etc/X11/xinit/xinitrc ~/.xinitrc

    $ vim ~/.xinitrc
    # load Xresources
    [[ -f ~/.Xresources ]] && xrdb -merge -I$HOME ~/.Xresources
    # start pulseaudio
    exec pulseaudio --start &
    # set DISPLAY
    systemctl --user import-environment DISPLAY
    # start ssh-agent
    eval $(ssh-agent)
    # desktop
    exec i3

And start it:

    $ startx

