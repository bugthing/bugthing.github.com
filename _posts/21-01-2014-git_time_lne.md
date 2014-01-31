---
layout: default
categories: blog
tags: vim git
title: See a files git history in vim
---

Discovered an awesome vim plugin today, it is called _git-time-lapse_

[Get it here](https://github.com/vim-scripts/git-time-lapse)

I was trying to track down where a bug had come from and wanted to see what had happened to
a perticular file over time, after a quick google I discovered git-time-lapse.

I installed in via Vundle (the vim plugin manager), by adding this into my .vimrc config:

    Bundle 'vim-scripts/git-time-lapse'

While you have .vimrc open, you might as well map a key combo to open fireup the plugin:

    map <leader>gt :call TimeLapse()<CR>

Then I opened vim and did the old

    :BundleInstall

I was them able hit my combo and vim would split into 3 panes (2 vertical and 1 horizontal at the bottom)

The 2 panes are basically vimdiff showing the changes to the file between commits and the bottom one has
the commit message... well handy!

It is now possiable to step through the file history using the left and right arrow keys, moving into the
different windows in the usual vim way (for me, that is Ctl+w and a direction)
