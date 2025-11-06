---
layout: default
title: Bluefin and homebrew ruby installation
---
Currently I am using [Bluefin](https://projectbluefin.io/) and its been very stable.

One issue I ran into was that ruby-build could not find the openssl libs when I was compiling ruby at the OS level (not in a container).

I noticed when it was compliling the following:

      ==> Installing ruby-3.4.7...
      ruby-build: using libyaml from homebrew
      ruby-build: using gmp from homebrew
      -> ./configure "--prefix=$HOME/.asdf/installs/ruby/3.4.7" --enable-shared --with-libyaml-dir=/home/linuxbrew/.linuxbrew/opt/libyaml --with-gmp-dir=/home/linuxbrew/.linuxbrew/opt/gmp --with-ext=openssl,psych

I am not sure why it noticed libyaml and gmp come from homebrew and not openssl, but it gave me a clue to see if I could force it to.

As quick search reminded about the configure options and I therefore set a enviroment variable to instruct ruby-build to use certain options.

  RUBY_CONFIGURE_OPTS="--with-openssl-dir=$(brew --prefix openssl@3)" asdf install  ruby latest

Ruby was now configured with the correct openssl lib
