---
layout: default
title: Ignore git files without .gitignore
---
I had the requirement to ignore a directory but not complicate things by having to add to .gitignore file

I discovered that in the .git directory I can maintain a list very similar to .gitignore but it does not need to be commited.

Give it a try and edit the following file in your repo:

    .git/info/exclude


