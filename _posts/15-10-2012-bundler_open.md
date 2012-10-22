---
layout: default
published: true
categories: blog
tags: ruby bundler
title: Show me the source code .. please bundler
---

One of the things I really missed when moving from Perl to Ruby was the knwledge of
how CPAN and dependancy chain works ... well things are starting to fit into place
(in my brain) abit more and 1 tip I want to write down is:

    bundle open <GEM>

This command will open the correct directory/file corrosponding to the named
gem.

This not only means you can check the source code, but you can find out which gem
bunlder thinks it should load for you project.
