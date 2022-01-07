---
layout: default
title: CoreOS with Kubernetes on bare metal
---

I use a raspberrypi as a home server, it runs a few docker containers and fulfills a number of roles. (web server, media server, CCTV hub, pihole, etc.)

After adding [JellyFin](https://jellyfin.org) it was apparent the RPI was under powered (I also wanted to add more disk for backups), so I set about configuring an old PC to do the same job.

The following are my notes on how I installed and configured the PC with CoreOS operating system and Kubernetes to run containers.

To set the scene, I operated on the PC from my laptop both of which were on the same LAN.

CoreOS Installation
-------------------

[Downloaded and burnt to USB](https://getfedora.org/coreos/download?tab=metal_virtualized&stream=stable)

[Produced YAML file of config](https://docs.fedoraproject.org/en-US/fedora-coreos/producing-ign/#_writing_the_butane_config)
this config sets a ssh key, hostname, console message level and the kubernetes yum repo. (add you ssh public key inplace of `<secret>`)

      tee home-server.yml <<EOF
        variant: fcos
        version: 1.4.0
        passwd:
          users:
            - name: core
              ssh_authorized_keys:
                - <secret>
        storage:
          files:
            - path: /etc/sysctl.d/20-silence-audit.conf
              mode: 0644
              contents:
                inline: |
                  kernel.printk=4
            - path: /etc/hostname
              mode: 0644
              contents:
                inline: |
                  baller
            - path: /etc/yum.repos.d/kubernetes.repo
              mode: 0644
              contents:
                inline: |
                  [kubernetes]
                  name=Kubernetes
                  baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
                  enabled=1
                  gpgcheck=1
                  repo_gpgcheck=1
                  gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
                  #exclude=kube*
      EOF

Convert the above to an ignition file which coreos-installer expects.

      docker run -it --rm -v $PWD/home-server.yml:/home-server.yml quay.io/coreos/butane:release --pretty --strict /home-server.yml > home-server.ign

Make .ign file available from my laptop by starting a simple webserver running my laptop.

      python -m http.server

Boot machine from USB drive and issue installation command from the live enviroment on the PC and pointing it at the webserver running on my laptop (the IP of which is 10.0.0.21)

      sudo coreos-installer install /dev/sda --ignition-url http://10.0.0.21:8000/home-server.ign --insecure-ignition

Kubernetes
----------

Now the operating system is on the PC, I can set about installing kubernetes

Install the kubernetes tools and reboot

      sudo rpm-ostree install -r kubelet kubeadm kubectl

Deal with SELinux by disabling it. (this is seems wrong but works)

      sudo setenforce 0
      sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

kubernetes requires a CNI (container network interface), I found I had to create the place for its config

      sudo mkdir -p /etc/cni/net.d
      sudo chmod 777 /etc/cni/net.d

Enable the services required at startup time (as per onscreen warnings when we initialise kubernetes)

      sudo systemctl enable kubelet.service
      sudo systemctl enable docker.service

Configure network to allow the behaviour require by k8s networking

      sudo tee /etc/sysctl.d/k8s.conf <<EOF
      net.bridge.bridge-nf-call-iptables  = 1
      net.ipv4.ip_forward                 = 1
      net.bridge.bridge-nf-call-ip6tables = 1
      EOF
      sudo sysctl --system

Initialise k8s

      sudo kubeadm init --pod-network-cidr=10.244.0.0/16

Follow the instructions from the above command, which is basically this:

      mkdir -p $HOME/.kube
      sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
      sudo chown $(id -u):$(id -g) $HOME/.kube/config

As CoreOS has read-only /usr I needed to change the flexvolume-dir (/usr/libexec to /etc) settings in the config, so do this:

      sudo mkdir -p /etc/kubernetes/kubelet-plugins/volume
      sudo sed -i 's_/usr/libexec_/etc_g' /etc/kubernetes/manifests/kube-controller-manager.yaml
      sudo systemctl restart kubelet.service

Add the pod networking stuff, in this instance we are using flannel:

      kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

.. some binarys are not in the place flannel expects, so lets create links to all the cni binarys that come with CoreOS

      for f in /usr/libexec/cni/*; do n="${f##*/}"; sudo ln -s $f /opt/cni/bin/$n; done

Untaint the master so it can run pods

      kubectl taint nodes --all node-role.kubernetes.io/master-

Test k8s
--------

Having installed k8s, lets see it if can run a test container

      kubectl create deployment hello --image=nginx
      kubectl expose deployment hello --type NodePort --port=80
      kubectl get svc

The above will display a port number assigned to the "hello" service, lets use that port to see if can see a webpage. Give it some time, as it took a few seconds to come up.

      curl http://$(hostname):31967

Misc. Commands
--------------

The time was not correct after I installed the OS, so I need to do this:

      timedatectl set-ntp yes

The inginition file did not set the hostname, so I had to do the following but I must have messed up, coz it works now as part of the initial install.

      hostnamectl set-hostname baller

