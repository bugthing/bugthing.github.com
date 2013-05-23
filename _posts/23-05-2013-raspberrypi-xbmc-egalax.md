---
layout: default
categories: blog
tags: linux raspberrypi 
title: A RaspberryPi touchscreen 
---

I have created a RaspberryPi based touchscreen with a wooden frame, this is roughtly how I did it.

I was given an old Acer 9000 Laptop that would not power on (I think the motherboard was dead) .. I was going to throw
it away but I thought that there must be something I can do with the screen from the laptop.

My next thoughts were RaspberryPi and touchscreen... so I purchased:
* RaspberryPi
* 4GB SD Card
* 17" USB Touchscreen Panel
* Controller board for Laptop screen

I got my friend who is a carpenter to build a nice wooden frame for my touchscreen so it can 
sit nicely in my living room without looking too out of place.

![RaspberryPi Frame](/images/pi_wood_work.jpg)

I made sure my controller board worked by plugging it into my ubuntu laptop and testing I could use
the old laptop screen as a 2nd monitor.

I then checked the USB touchscreen panel by pluggin that into my ubuntu laptop.. it worked prefectly
out the box!

So I then put everything together... and set about getting it all to work on a RaspberryPi

![RaspberryPi back](/images/back_of_raspberrypi_touchscreen.jpg)
![RaspberryPi front](/images/front_of_raspberrypi_touchscreen.jpg)

I struggled for ages until I saw 
[this](http://www.engineering-diy.blogspot.ro/2013/02/raspberry-pi-raspbian-xbmc-and-egalax-7.html)
posting (thank god!)

I followed the instructions which basically involved:

* Installing Raspbian.
* Building a kernel with eGalax support
  * Configure the kernel
  * Build the kernel
  * Overwrite the Raspbian kernel on SD with the new kernel
* Building XBMC with touchscreen alterations
  * Apply patches to XBMC source code
  * compile xbmc
* Calibrating
  * in "/usr/share/eGalaxCalibration/touchscreen_axes_calib"
* Building XBMC with MORE touchscreen alterations
  * To fix Axes that are inverted, change:
  *   pointer.y = screen_height - value_read.y * calib_y_fact - calib_y_d;
  * in "xbmc/input/linux/LinuxInputDevices.cpp" to:
  *   pointer.y = value_read.y * calib_y_fact - calib_y_d; .
  * NOTE: search for "calib_" and you will find 2 similar lines.
  * recompile xbmc
* Calibrating

Now I have nice looking touchscreen to control the media in my living room

