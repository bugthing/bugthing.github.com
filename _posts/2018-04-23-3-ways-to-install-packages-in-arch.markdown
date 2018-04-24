---
layout: default
title: More ways to install packages in arch
---

Arch moves pretty fast and is really stable considering.

I find a good way to keep things stable is by keeping my install as simple as possible, meaning
I get the bare minimum from the repos to run a fast secure os. This means updates do not get
too overwelming or risky.

But how do you get software if you are trying to keep your system thin?
Here I will explore some methods I have used.

Some key things I aim for are:
* install as own user (root permissions can be dangerous)
* self contained (not putting files all over the place)
* self updating

## AppImage

Appimage is a basically 1 file that contains all required dependancies to execute.
So you can download and run:

    $ wget https://github.com/zeit/hyper/releases/download/2.0.0/hyper-2.0.0-x86_64.AppImage
    $ chmod +x hyper-2.0.0-x86_64.AppImage
    $ ./hyper-2.0.0-x86_64.AppImage

As it is completely self contained you'll want something to integrate the appimage
applications within your desktop.
For this there is *appimaged* https://github.com/AppImage/AppImageKit#appimaged-usage
Currently this image needs tweaking before it will run in arch, so use AUR:

    $ git clone https://aur.archlinux.org/appimage-git.git
    $ cd appimage-git

I had to tempory tweak my makepkg.conf like so:

    $ sudo vim /etc/makepkg.conf
    #CFLAGS="-march=x86-64 -mtune=generic -O2 -pipe -fstack-protector-strong -fno-plt"
    CFLAGS="-march=x86-64 -mtune=generic -O2 -pipe -fstack-protector-strong -fno-plt -lz"

    $ makepkg -si
    $ mkdir ~/Application
    $ appimaged --install

Now you can download app images to the directory *~/Applicationa*, make them executable
and theyh magically appear in your desktop menus.
Currently I know of no way to auto update appimages.

## flatpak

Flatpak is simlar to appimage, except it has a support package to help manage packages
and integrate them into your desktop.

    $ sudo pacman -S flatpak

You can add repositories to get software from:

    $ flatpak remote-add --user --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
    $ flatpak install --user flathub org.gimp.GIMP

## snappy

Snappy is from ubuntu and has a service running on the host system that helps manage and integrate packages.

    $ git clone https://aur.archlinux.org/snapd.git
    $ cd snapd
    $ makepkg -si
    $ sudo systemctl enable snapd
    $ sudo systemctl start snapd

Search for and install software as you please

    $ snap search chromium
    $ sudo snap install chromium

## Docker

Docker can be handy to get software running

    $ sudo pacman -s docker
    $ sudo gpasswd --add $USER docker
    $ newgrp
    $ sudo systemctl enable docker
    $ sudo systemctl start docker

Now you can install software into docker containers. Here I run a postgres server
in a container and also start a client in a container to connected to it.

    $ docker run -d --name postgres -p 5432:5432 -e POSTGRES_PASSWORD=postgres postgres
    $ docker run -it --rm --link postgres postgres psql -h postgres -U postgres -W template1

## asdf

Sometimes programming languages have specific tools which can be installed using the
languages own package manager. (e.g. npm for javascript, gem for ruby)
Using a custom installed language installation means you can install these packages without
going to root and also you can blow them away without screwing with your system.

    $ git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.4.3
    $ echo -e '\n. $HOME/.asdf/asdf.sh' >> ~/.bashrc
    $ echo -e '\n. $HOME/.asdf/completions/asdf.bash' >> ~/.bashrc
    $ source ~/.bashrc
    $ asdf plugin-add ruby https://github.com/asdf-vm/asdf-ruby.git
    $ asdf plugin-add nodejs https://github.com/asdf-vm/asdf-nodejs.git
    $ asdf plugin-add elixir https://github.com/asdf-vm/asdf-elixir.git
    $ asdf plugin-add python https://github.com/tuvistavie/asdf-python.git
    $ asdf plugin-add java https://github.com/skotchpine/asdf-java
    $ asdf plugin-add elm https://github.com/vic/asdf-elm.git
    $ asdf plugin-add crystal https://github.com/marciogm/asdf-crystal.git

