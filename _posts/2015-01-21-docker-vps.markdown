---
layout: default
categories: blog
tags: linux docker vps
title: Docker on my VPS
---
So I got fed up with Heart Internet, they do not rock my world, so looked around and seeing as [ByteMark](https://www.bytemark.co.uk/) sponser alot of FLOSS stuff I thought I'd give'm a try.
I was tempted by DigitalOcean as they are so cheap, but it would make me feel like a sell-out.

All I want is a cheap reliable linux machine, and that is exactly what I got.

Now I have a VPS at a new host, **how do I get it to all the things running on the new host?**

Over time had setup the old host to serve Rails apps, email server, html sites, php sites, ftp, etc... you I might think **"mission!"**

.. but no, the answer is [Docker](https://www.docker.com/)

I built a container for [nginx](https://github.com/bugthing/docker-nginx) and [opensmptd](https://github.com/bugthing/docker-opensmtpd) and I was pretty much done. I could make use of [other containers](https://registry.hub.docker.com/) for other services

All I had to do was install the following onto my Ubuntu server (I really wanted Debian, but guess Ubuntu will do)

* Docker
* ufw
* fail2ban

The just had to

* install my ssh key onto the box
* configure ssh to only allow key access
* setup ufw to only allow web, smtp, etc.
* add my user to the 'docker' group
* start containers to serve smtp, web, etc.

I now a have a simple, easy to manage, fairly secure VPS. Now that everything is running under Docker I can transfer everything to any Linux box should I feel I want to move... RAD!
