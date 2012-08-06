---
layout: default
categories: blog
tags: ubuntu rivendell
title: Ubuntu Studio 12.04 + Rivendell 
---
One of my mates has a girl friend who wants to get into radio.
She does amature radio stuff in Milton Keynes I think its called "community radio"

I think she should do a podcast. she is a cool cat and I am sure people would
want to hear what she has to say on stuff. 

With the above in mind I would like to setup her up with a home studio... on the
cheap! (I'm not made of money!)

So I dugg out an old P4 machine I had and installed Ubuntu Studio 12.04 on it... 
I also had an real old SoundBlaster Live, so I stuck that in it too.

I have not seen this distro for a few years and its looking cool, there is a load
of software on it that all looks great for anyone wanting to edit audio/video.

I want to setup Rivendell, which is radio automation software... as ever a tiny bit
of googling ends me up with a real nice step by step guide to what to do.. sweet :)

[Guide](http://bluedrava.com/rivendell-on-ubuntu-12.04)

[PDF](/etc/Rivendell_2_on_Ubuntu_1104.pdf)

[Rivendell](http://www.rivendellaudio.org/)

[More help](http://rivendell.tryphon.org/wiki/Install_Rivendell_on_Ubuntu)

Once I ran through the guide, which went very smoothly I was left with a machine
nearly ready to be used... but alas, typically there are problems :(

Jack seems to be throwing errors.. which I think is related to the latacy times...
.. its a very old slow machine .. so I am looking at speeding the hardware abit before
I start to get into a software solution...

So my hardware was fine (I thought it might be a problem being as it pretty old)
but it was not, it was the sample rate that I had not set ..  I tweaked things 
and Jack and Rivendell now work nicely

I did have trouble with PulseAudio and Jack... Audacious was not playing when jack
was running (it was sorted jammed) .. I had to open Paluse Audio mixer and 
tell Audacious to use Jack sink as its ouput.

Once its ready, I'll copy of music/jiggles over, put some demo podcast on and 
take it over to my pals house and see if his misses wants to start a pod cast 
or radio show.



Copy of guide here:
===================

* Step 2  - ignored, wanted stock Ubuntu repos rather than KXStudio.
 
* Step 3  - libjack0.100.0-dev is included in libjack-dev
 
* Step 4  - Pulse module already installed
 
* Step 8  - I had problems using 4 periods, stuck to 2. (Might be hardware). Uncheck confirm close.
 
* Step 12 - QT compile problem in 11.10 onwards. [Solution found](http://www.thevoiceasia.org/rivendell/Rivendell_2_on_Ubuntu_1104.pdf)

    Find this line:
        bnv_try_4="$CXX $QT_LIBS $LIBS -o bnv_qt_main bnv_qt_main.o moc_bnv_qt_test.o >/dev/null 2>/dev/null"
    Change it to this:
        bnv_try_4="$CXX -o bnv_qt_main bnv_qt_main.o moc_bnv_qt_test.o $QT_LIBS $LIBS >/dev/null 2>/dev/null"
    Then continue with this:
        ./configure -libexecdir=/usr/local/libexec --with-Qt-dir=/usr/share/qt3

* Step 14 - Looks like the shortcuts appear after the make install, don't bother with copying them unless they haven't appeared.
 
* Step 15 - Skipped as it's purely cosmetic and I never noticed the difference really.
 
* Step 16 - Your user is probably already part of the audio group but there's no harm in making sure.
 
* Step 17 - Don't use mysql root password, this is just rivendell talking to it's own db. You only need the root password when first run and the rivendell user and database is being created.
 
* Step 18 - I skipped this as it makes the jack output rivendell_2 rather than rivendell_0.
 
* Step 19 - sudo cp ~/rivendell-2.0.2/conf/rd-bin.conf /etc/apache2/conf.d/
           This doesn't seem to work as intended, The conf might have to go into "sites-enabled" but I've yet to find the information on using the web interface.
 
* Step 21 - make secure root pass, only need to use it once to set up rd database
 
* Step 25-26 - The rws thing didn't effect rivendell connecting so I left it.

