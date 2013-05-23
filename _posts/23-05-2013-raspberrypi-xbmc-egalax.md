---
layout: default
categories: blog
tags: linux raspberrypi 
title: A RaspberryPi touchscreen 
---

I have created a RaspberryPi based touchscreen with a wooden frame, this is roughtly how I did it.

I was given an old Acer 9000 Laptop that would not power on (I think the motherboard was dead) .. I was going to throw
it away but I thought that there must be something I can do with the screen from the laptop.

I took the laptop apart and removed just the screen and the little boards that were connected via small
cables... on the back there was the manufactor and the model number.

Google told me that I would need a controller board to be able to plug the screen into a computer, I found
the correct one on ebay using the model number.

The controller board and screen worked as an extra monitor when plugged into my laptop which was cool, but
then I thought about a RaspberryPi and touchscreen!... so I purchased:
* RaspberryPi
* 4GB SD Card
* 17" USB Touchscreen Panel

I then checked the USB touchscreen panel by plugging that into my ubuntu laptop.. it worked prefectly out the box!

Next I got my friend who is a carpenter to build a nice wooden frame hold the screen and the touch panel together
and also so it can sit nicely in my living room without looking too out of place.

![RaspberryPi Frame](/images/pi_wood_work.jpg)

Next I put everything together.  Seeing as the RaspberryPi and Controller board are small it all fits behind 
the screen within the frame .. perfect!

Now I set about getting it all to work on a RaspberryPi

![RaspberryPi back](/images/back_of_raspberrypi_touchscreen.jpg)
![RaspberryPi front](/images/front_of_raspberrypi_touchscreen.jpg)

The standard Raspbian install works well with the screen but the touch pannel does not work.. bummer!

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
  * To fix Axes that in my case were inverted, change:
  *   pointer.y = screen_height - value_read.y * calib_y_fact - calib_y_d;
  * in "xbmc/input/linux/LinuxInputDevices.cpp" to:
  *   pointer.y = value_read.y * calib_y_fact - calib_y_d; .
  * NOTE: search for "calib_" and you will find 2 similar lines.
  * recompile xbmc
* Calibrating

Now I have nice looking touchscreen to control the media in my living room

