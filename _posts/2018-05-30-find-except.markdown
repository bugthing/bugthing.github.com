---
layout: default
title: Find -exclude
---

I was looking for a `--exclude` flag in the gnu find tool, there is not one, for good reason I
guess but I wanted it without having to dive in and learn about `-prune` (you lazy bastard!),
so anyway, I did this:


       $ find ./dir/ -not -path "./dir/to/exclude/*" -name things_to_find*

