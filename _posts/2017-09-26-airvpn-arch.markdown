---
layout: default
title: AirVPN on Arch
---

I wanted a VPN for use at home, so I did 1 second of googling a decided upon [AirVPN](https://airvpn.org/?referred_by=366370)

I was hoping I could just use the Gnome NetworkManager GUI to configure it, but that did not work, I had to use the cli openvpn tool.

On my ArchLinx laptop I install some packages like so:

    $ pacaur -S openvpn-update-resolv-conf openvpn

Next I signed up [here](https://airvpn.org/?referred_by=366370)

Once logged into the AirVPN site, I went to *Client Area* -> *Config Generator*

Selected Linux and chose my area to vpn into (Europe)

After agreeing the T&C's I was able to download a config file (AirVPN_Europe_TCP-443.ovpn)

I altered to config file and added (after "verb 3"):

```
script-security 2
setenv PATH /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
up /etc/openvpn/scripts/update-systemd-resolved
down /etc/openvpn/scripts/update-systemd-resolved
down-pre
```

Next I used a systemd service to handle dns stuff

```
git clone https://github.com/jonathanio/update-systemd-resolved.git
cd update-systemd-resolved
sudo mkdir /etc/openvpn/scripts
sudo chmod 777 /etc/openvpn/scripts/
make
systemctl enable systemd-resolved.service
systemctl start systemd-resolved.service
```

I also discovered I had to tweak NetworkManager.conf (as I use that on my linux desktop)
so I open `/etc/NetworkManager/NetworkManager.conf` and add `dns=none` to the `main` section

```
[main]
dns=none
```

I then used to following command, which configured my machine to send network traffic over the VPN

    $ sudo openvpn ./AirVPN_Europe_TCP-443.ovpn

