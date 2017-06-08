---
layout: default
categories: blog
tags: linux cli
title: SSH Forward + IPTables
---

When making changes to our project, we commit into git and then build the whole app, this includes multiple services. For this we make use of docker and selenium.

Recently the selenium based tests started to fail but pass locally... I needed to debug.

The docker containers are on a machine behind a ssh gateway, so it was hard to access the web UI to see if things looked ok.

Here is what I did.

Configured ssh

    $ cat ~/.ssh/config

    Host bastion
      User myuser
      Hostname blha.company.com
    Host ciserver
      User myuser
      Hostname 10.10.10.2
      ProxyCommand ssh -q -A -x bastion -W %h:22
    Host ci-container-1
      User root
      Hostname 0.0.0.0
      ProxyCommand ssh -q -A -x ciserver -W %h:2221

Connect with a local forward

    $ ssh -L 8080:127.0.0.1:80 ci-container-1

Use iptables to forward port 80 to 8080

    $ sudo iptables -t nat -I OUTPUT --src 0/0 --dst 127.0.0.1 -p tcp --dport 80 -j REDIRECT --to-ports 8080

Now I can visit the web ui within the container from my machine

    $ wget http://webui.127-0-0-1.org.uk/

If you want to remove the firewall rule once your done, use this:

First find out the chain and line number for the rule you created

    $ iptables -t nat -vnL --line-numbers

Then delete it

    $ iptables -t nat -D OUTPUT 12345

