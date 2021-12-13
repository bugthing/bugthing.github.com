---
layout: default
title: Using the fondo flatpak to set feh wallpaper
---

I wanted to use the nice linux desktop app [Fondo](https://github.com/calo001/fondo) but I use `feh` to handle my wallpaper display and therefore I believe this would not work.

I noticed the Flatpak app stored the wallpapers in a certain directory, so I thought I could monitor that dir, if a new file is added, I can get tell `feh` to set it as my wallpaper.

These are steps I took


## Build the script to read Fondon flatpak dir and set as wallpaper

Created a shell script in my $HOME directory.

      vi $HOME/.fehbg

give the file the following content

      #!/bin/bash
      feh --no-fehbg --bg-fill "$(find $HOME/.var/app/com.github.calo001.fondo/data/backgrounds -type f -printf '%T@ %p\n' | sort -rz  | sed -En 's/[^ ]* //p' | tail -1)"

## The .service file

Now i needed systemd to execute the file, so it needs a service file, therefore I created a .service file in my $HOME directory.

      vi $HOME/.config/systemd/user/feh-fondo-wallpaper.service

and added the following content to the file.

      [Unit]
      Description="Run script to set wallpaper via feh"
       
      [Service]
      ExecStart=%h/.fehbg

## The .path file

Now systemd can execute my script, I need to tell it do so when changes happen in the flatpak directory, this can be done with a .path file, which I created in my $HOME directory.

      vi $HOME/.config/systemd/user/feh-fondo-wallpaper.path

and added the following content to the file.

      [Unit]
      Description="Monitor a dir for changes"
      
      [Path]
      PathModified=%h/.var/app/com.github.calo001.fondo/data/backgrounds
      Unit=feh-fondo-wallpaper.service
      
      [Install]
      WantedBy=multi-user.target

Check for typos etc.

      systemd-analyze verify $HOME/.config/systemd/user/feh-fondo-wallpaper.*

Start the service

      systemctl start --user feh-fondo-wallpaper.path
      systemctl enable --user feh-fondo-wallpaper.path

