---
layout: default
title: Fedora VM Networking (libvirt)
---
I have a home server running the excellent [UCore](https://github.com/ublue-os/ucore/). The `hci` edition provides all the
software and base configuration to run containers and virtual machines with ease.

Using [Cockpit](https://cockpit-project.org/), I was to be able to spin up VMs with ease, my issue was with networking.
The default creates a NAT configuration which then requires special handling to access the VM from the LAN, I wanted this
to be easier.
I wanted my VMs to use my existing network to get IP addresses and access the internet, as if I'd plugged them into the
switch with cat5 cable, this was more tricky than I thought.

I ended up putting a second network card in the server and using it as "bridge" network so my VMs can use that phyical card
to access the network, whilst the server can still use the existing card as before.
There was much trial and error as I'm no good at networking, but it ended up being these steps:

Find the interface name of the new network card:

```
$ ip a # in my case it was enp5s0
```

Remove any existing network configuration for that card:

```
$ nmcli con    # (Get the connection name)
$ nmcli con delete "Wired connection 2"
```

Create a new bridge connection and add the interface to it:

```
$ nmcli con add con-name br0 ifname br0 type bridge autoconnect yes ipv4.method auto ipv6.method auto
$ nmcli con add con-name br0-port ifname enp5s0 type ethernet slave-type bridge master br0 autoconnect yes
```

create a file `bridged-network.xml` with the following content:

```
<network>
    <name>bridged-network</name>
    <forward mode="bridge" />
    <bridge name="br0" />
</network>
```

Import the bridge network into libvirt:

```
virsh net-define bridged-network.xml
```

With the above in place (and the server rebooted) I was able to use Cockpit to select the newly added `bridged-network`
and assign it to the VMs as a "Bridged to LAN" type interface, and they VMs could then get network access and DHCP from
my router.
