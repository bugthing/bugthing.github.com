bugthing.github.com
===================

bugthing owned github page.

I wanted a place to put notes regarding the techy stuff, github.com seems like a good choice.

Install
-------

    bundle install

Serve
-----

    bundle exec jekyll serve --watch

Start a new post
----------------

    NEWPOST="./_posts/$(date +"%d-%m-%Y")-something.md"; head -6 _posts/01-08-2012-sudo.md > $NEWPOST; vi $NEWPOST

