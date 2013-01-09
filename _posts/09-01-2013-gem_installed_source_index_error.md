---
layout: default
categories: blog
tags: linux cli 
title: Oldskool gem error
---

There is an old project we maintain at work.. it runs on a old version of rails (2.3.14)
.. we had to update to 2.3.15 to fix a manjor Rails security [hole](http://www.insinuator.net/2013/01/rails-yaml/)

As a knock on effect, I had to update from some old version of rack to 1.1.4 (still old, but abit newer)

This gem is in 'vendor/gems' .. so I updated, but when starting the server I got this:

    "config.gem: Unpacked gem rack-1.1.4 in vendor/gems has no specification file. Run 'rake gems:refresh_specs' to fix this."

When I did as the message tells me to, I get:

    undefined method `installed_source_index' for #<Gem::SourceIndex:0x7f1500ea22e0>

After abit of Googling I was still at a loss... but thankfully my college let me know about this (which fixed it :)

    bundle exec gem  specification  rack > vendor/gems/rack-1.1.4/.specification

\o/
