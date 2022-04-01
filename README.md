# Raspberry Pi Local Services Cluster
## Overview
## Equipment
## Preparation (headless installation)
### Prerequisites
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
1. Download [Rancher Desktop](https://docs.rancherdesktop.io/getting-started/installation).