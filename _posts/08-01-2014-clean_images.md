---
layout: default
categories: blog
tags: linux cli
title: Clean your photos before you upload
---

As you probably know digital photos contain meta data about when/where/what/etc took the snap.

Before you upload or send digital piccies you might want to remove this data to prevent tracking, I do this:

    jhead -mkexif IMAGE.jpg

That clears out most data..to remove it all do this:

    jhead -purejpg IMAGE.jpg

You can see what data is held in an image, like so:

    jhead IMAGE.jpg

