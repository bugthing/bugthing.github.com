---
layout: default
categories: blog
tags: linux svn cli 
title: SVN - Merge a branch into trunk
---

Unfortunatly I sometimes still have to use Subverison.

I always have to google for "svn merge branch into trunk".. so I thought I'd place the basics here:

- Change dir into the trunk svn place.

- Get the revision number when the branch you want to merge diverted from trunk (lets call that number AAAA)

        svn log --stop-on-copy https://svn.workplace.com/projects/myproject/branches/feature_branch

- Get the most recent revision number from trunk (lets call that number BBBB)
    
        svn update

- Do the merge

        svn merge -rAAAA:BBBB https://svn.workplace.com/projects/myproject/branches/feature_branch

- Fix all the conflicts

- Commit when your done

        svn checkin -m "MERGE: myproject feature_branch AAAA:BBBB into trunk"



