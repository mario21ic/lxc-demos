### Containers ###
```
lxc launch ubuntu:24.04 first
lxc list
lxc exec first -- /bin/bash
lxc exec first -- apt update
lxc stop first
lxc start first
lxc delete second --force
lxc file pull first/etc/hosts .
lxc file push hosts first/tmp/

lxc launch ubuntu:20.04 u20
lxc exec u20 -- cat /etc/*release
```

### Virtual Machines ###
```
lxc launch ubuntu:22.04 third --vm
lxc exec third -- bash
```

### Repositories ###
```
lxc remote list
lxc remote add --protocol=simplestreams images-community https://images.linuxcontainers.org
```
That is not working anymore.
```
sudo apt install incus
incus image copy images:centos/10-Stream local: --alias centos10
incus image export centos10 /tmp/centos10
lxc image import /tmp/centos10* --alias centos/10-Stream
lxc launch centos/10-Stream ct-centos10
lxc exec ct-centos10 -- bash
```

### Snapshot ###
```
lxc launch ubuntu:24.04 second
lxc snapshot second default-installation
lxc info second

lxc exec second -- yum install git -y
lxc exec second -- git --version
lxc snapshot second git-installed
lxc info second

lxc restore second default-installation
lxc exec second -- git --version

lxc restore second git-installed
lxc exec second -- git --version

lxc copy second/git-installed mycentos-git
lxc list
```

### Images ###
```
lxc image list
mkdir -p my-ubuntu/rootfs
lxc image export ubuntu:18.04 my-ubuntu
ls -la my-ubuntu
tar -xvf my-ubuntu/ubuntu-18.04-server-cloudimg-amd64-lxd.tar.xz -C my-ubuntu
tree my-ubuntu/
lxc image import my-ubuntu --alias my-custom-ubuntu
lxc image list
```

### Networking with LAN ###
```
$ sudo vim /etc/netplan/01-bridge.yaml
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: false

  bridges:
    br0:
      interfaces: [ens18]
      addresses: [192.168.2.110/24]
      routes:
        - to: default
          via: 192.168.2.1
      nameservers:
        addresses: [1.1.1.1, 8.8.8.8]
      parameters:
        stp: false
        forward-delay: 0

$ lxc profile edit lan-profile << 'EOF'
config: {}
description: Perfil con IP en LAN via macvlan
devices:
  eth0:
    name: eth0
    nictype: bridged
    parent: br0
    type: nic
  root:
    path: /
    pool: default
    type: disk
name: lan-profile
EOF
$ lxc launch ubuntu:24.04 ct-lan --profile lan-profile
$ lxc exec ct-lan -- bash
# ip addr
```

### Profile ###
```
lxc profile list
lxc profile copy default devprofile
lxc profile show devprofile
lxc profile edit devprofile # like devprofile.yaml
lxc launch --profile devprofile ubuntu:18.04 mydev
lxc exec mydev -- cat /etc/timezone
lxc exec mydev -- ls /tmp/
lxc exec mydev -- git --version
lxc exec mydev -- nmap --version
lxc exec mydev -- cat /var/log/syslog
lxc exec mydev -- cat /var/log/cloud-init.log |grep "git"
lxc exec mydev -- cat /var/log/cloud-init.log |grep "nmap"
```


### init ###
```
$ sudo lxd init
Would you like to use LXD clustering? (yes/no) [default=no]: no
Do you want to configure a new storage pool? (yes/no) [default=yes]: 
Name of the new storage pool [default=default]: 
Name of the storage backend to use (dir, lvm) [default=dir]: 
Would you like to connect to a MAAS server? (yes/no) [default=no]: 
Would you like to create a new local network bridge? (yes/no) [default=yes]: 
What should the new bridge be called? [default=lxdbr0]: 
What IPv4 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]: 
What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]: 
Would you like LXD to be available over the network? (yes/no) [default=no]: 
Would you like stale cached images to be updated automatically? (yes/no) [default=yes] 
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]:
```

