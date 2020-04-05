lxc launch ubuntu:18.04 first
lxc list
lxc image list
lxc exec first -- /bin/bash
lxc exec first -- apt update
lxc file pull first/etc/hosts .
lxc file push hosts first/tmp/

mkdir -p my-ubuntu/rootfs
lxc image export ubuntu:18.04 my-ubuntu
ls -la my-ubuntu
tar -xvf my-ubuntu/ubuntu-18.04-server-cloudimg-amd64-lxd.tar.xz -C my-ubuntu
tree my-ubuntu/
lxc image import my-ubuntu --alias my-custom-ubuntu
lxc image list


lxc launch images:centos/7/amd64 second
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

lxc stop second
lxc delete second --force


