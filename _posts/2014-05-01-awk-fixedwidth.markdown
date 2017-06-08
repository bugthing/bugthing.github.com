---
layout: default
categories: blog
tags: linux cli
title: Fixed width file processing with awk
---

Sadly some old skool systems are still in use and therefore I get the odd
fixed width file to deal with.

For quick command line processing I find 'awk' pretty handy, so for example I might run:

    sed -n 10p old_skool.txt | awk 'BEGIN { FIELDWIDTHS="3 3 6 8 12 24" } { print NR " - " $2 " - " $4 }'

The above will take line 10 from 'old_skool.txt'
Split it up using column lengths 3 3 6 8 12 24
Print the line number, a dash, column 2, another dash and column 4
