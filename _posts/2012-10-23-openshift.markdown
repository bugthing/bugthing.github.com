---
layout: default
published: true
categories: blog
tags: ruby github projects web
title: Got my project, but where does it go?
---

So I re-started an old idea of mine, this time using Ruby and Rails .. partly 
because I keep thinking it and how, if it was good, it could be applied to 
lots of situations, also because a rewrite in Ruby and Rails will help me learn.

Again
=====

So I started this idea while working at place that was writing a system to streamline
the scripts and data capturing at call center.

It was a good idea and I thought if I was to write it myself I could do it better, and
make it less specific to the call center market.

When I left that place of work I started to write it in the evening, using Perl,
Dancer and EmberJS .. after a few weeks of evenings I had something at sort of 
worked, but I got bord and abandoned my project (you can see my repos at 
github, Flowella)

Now I am working in Ruby and need to learn Rails better, I thought I would re-write it

Started
=======

So I started by creating a new rails project, adding a User model, a few tests and
applying Bootstrap... now there is 'something' .. basically just an empty shell
of a project but it is there on github (ProPro)

I wanted somewhere to host it .. my own VPS would be a good choice, but I heard about
OpenShift and wanted to try it.

OpenShift
=========

OpenShift is cloud App hosting from RedHat, it is free to try and you can pay if
want more power, but for starters it is excellent.

You create the 'App' on their web site and then get that App onto your machine 
using git... brillent!

So I created a test rails app at OpenShift, it all seemed good, so I made some 
changes like before (tests, user model, bootstrap) and then add another remote 
in git set to an repo at github and uploaded it.

I now have a great base to work from mosty in "the clound" .. source code open
at github, hosting at OpenShift.... this means if I actually finish the project 
this time, and if it ever gets used, I can expand at OpenShift with ease.. nice.


