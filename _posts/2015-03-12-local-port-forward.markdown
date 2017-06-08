---
layout: default
categories: blog
tags: linux cli 
title: Port forward locally
---

So I was trying to test out a web service deep inside a private network which I had to create a number of SSH tunnels
to access.

I used **LocalForward** to get at the port, but as it was a privilage port I could not create the forward without being `root`

I guess I could have setup ssh tunnels as root.. but it was already working, instead to did a local forward to a non-privilaged
port, then did used iptables to do a local port forward

    sudo iptables -t nat -A OUTPUT -p tcp -d 127.0.0.0/8 --dport 80 -j REDIRECT --to-port 5580
