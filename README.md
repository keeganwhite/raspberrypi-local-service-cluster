# Raspberry Pi Local Services Cluster
## Overview
This repo contains scripts and instructions on how to set up a K3s cluster on Raspberry Pis. The cluster will run
services that can replace Internet-based applications. Inspiration was drawn from my work for 
[iNethi](https://www.inethi.org.za/). You can find the full Docker-based iNethi local services repo 
[here](https://github.com/iNethi/master-builder).
## Equipment
1. Two or more Raspberry Pi 4s
2. A network switch
3. Ethernet cables (you can do it over WiFi but this guide is done over Ethernet)
4. An access point
5. A Pfsense firewall (optional)
## Preparation
### Configure Raspberry Pis
1. Flash your SD cards with Raspbian Pi OS Lite (32-bit)
2. Insert the SD cards into the Pis and boot them. Let them boot (wait a few minutes). Turn off the Pis and remove the
SD cards.
3. Open the SD cards on your machine and edit your ```cmdline.txt``` file, add the following:
```
3. cgroup_memory=1 cgroup_enable=memory ip=192.168.68.190::192.168.68.1:255.255.255.0:rpimaster:eth0:off
```
where **ip=** is the IP address you are manually assigning to your pi, ```192.168.68.1``` is your routers IP address, 
```rpimaster``` is the name you are giving your Pi and ```eth0``` is your Pi's NIC. Remember to edit the IP addresses
and name to be unique per Pi (and make sure they are not already assigned on your network!).
*Note: ip=<client-ip>:<server-ip>:<gw-ip>:<netmask>:<hostname>:<device>:<autoconf>
4. Open ```config.txt``` and add the following to the end of the file to make sure you use a 64 bit versions of Raspbian:
```
arm_64bit=1
```
5. Open the boot folder in your terminal and run:
```
touch ssh
```
This will enable ssh when you reboot the Pi.
6. Plug in the Pi's and ssh into them:
```
ssh pi@192.168.68.190
```
password = raspberry. 
6. Become root 
```
6. sudo su -
```
7. Install and enable iptables:
```
sudo apt-get install iptables
sudo iptables -F 
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy 
sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```
8. Reboot the Pi: 
```
reboot
```
#### Install K3s on the master node:
1. Install K3s
```
sudo su -
curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" sh -s -
```
2. Test it is working:
```
kubectl get nodes
```
3. Get the node token from the master node
```
sudo cat /var/lib/rancher/k3s/server/node-token
```
#### Install K3s on the other nodes:
1. Install K3s on your other nodes:
```
curl -sfL https://get.k3s.io | K3S_TOKEN="YOURTOKEN" K3S_URL="https://[your server]:6443" K3S_NODE_NAME="servername" sh -
```
where ```K3S_TOKEN``` is your master token, ```K3S_URL``` is your master's IP address and ```K3S_NODE_NAME``` is the 
name you want to assign to your new node 
2. Check everything is working from the master node:
```
kubectl get nodes
```
#### Install and set up Rancher
1. Download [virtual box](https://www.virtualbox.org/) and create and [Ubuntu server](https://ubuntu.com/download/server) 
VM.
2. ssh into your vm, become root and create the following directories:
```
mkdir /etc/rancher
mkdir /etc/rancher/rke2
```
3. Create a config file:
```
cd /etc/rancher/rke2
nano config.yaml
```
and add the following:
```
token: mysecrettoken
tls-san:
    - 192.168.68.200
```
where ```token``` is any random secret token and the ip address you enter is the ip of your VM
4. Enable the rancher service using systemclt:
```
systemctl enable rancherd-server.service
systemctl start rancherd-server.service
```
wait for the service to start. You can monitor it using the following:
```
journalctl -eu rancherd-server -f
```
5. Reset the admin password:
```
rancherd reset-admin
```