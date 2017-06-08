---
layout: default
categories: blog
tags: linux ruby apache
title: rbenv apache sinatra
---

I have to install a sinatra application I quickly knocked together for a client,
here is what I had to do to get it running on a production server.

Originally I installed ruby2 but discovered (at the time of writing) passenger3
could be installed.. so I reverted to ruby 1.9.3

Do everything as root
    sudo bash

Install some packages
---------------------
    apt-get update
    apt-get install zlibc zlib1g zlib1g-dev  build-essential libssl-dev libyaml-dev freetds-dev libcurl4-openssl-dev apache2-prefork-dev libapr1-dev libaprutil1-dev

Install rbenv to the system
----------------------------
    git clone git://github.com/sstephenson/rbenv.git /usr/local/rbenv
    echo '# rbenv setup' > /etc/profile.d/rbenv.sh
    echo 'export RBENV_ROOT=/usr/local/rbenv' >> /etc/profile.d/rbenv.sh
    echo 'export PATH="$RBENV_ROOT/bin:$PATH"' >> /etc/profile.d/rbenv.sh
    echo 'eval "$(rbenv init -)"' >> /etc/profile.d/rbenv.sh
    chmod +x /etc/profile.d/rbenv.sh
    source /etc/profile.d/rbenv.sh
    pushd /tmp
      git clone git://github.com/sstephenson/ruby-build.git
      cd ruby-build
      ./install.sh
    popd
    rbenv install 1.9.3-p392
    rbenv global 1.9.3-p392
    rbenv rehash

Install passenger
-----------------
    gem install passenger
    rbenv rehash
    passenger-install-apache2-module

Configure Apache
----------------
    vi /etc/apache2/conf.d/passenger.conf 
      LoadModule passenger_module /usr/local/rbenv/versions/1.9.3-p392/lib/ruby/gems/1.9.1/gems/passenger-3.0.19/ext/apache2/mod_passenger.so
      PassengerRoot /usr/local/rbenv/versions/1.9.3-p392/lib/ruby/gems/1.9.1/gems/passenger-3.0.19
      PassengerRuby /usr/local/rbenv/versions/1.9.3-p392/bin/ruby

    vi /etc/apache2/site-enabled/mysite.conf
      <VirtualHost *:80>
         ServerName www.yourhost.com
         # !!! Be sure to point DocumentRoot to 'public'!
         DocumentRoot /somewhere/public    
         <Directory /somewhere/public>
            # This relaxes Apache security settings.
            AllowOverride all
            # MultiViews must be turned off.
            Options -MultiViews
         </Directory>
      </VirtualHost>

Bundle
------
  cd /somewhere
  bundle


