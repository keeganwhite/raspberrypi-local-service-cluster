# Raspberry Pi Local Services Cluster
## Overview
## Equipment
## Preparation (headless installation)
### Prerequisites
1. Flash your SD cards with Raspbian Pi OS Lite (32-bit)
2. Insert the SD cards into the Pis and boot them. Let them boot (wait a few minutes). Turn off the Pis and remove the
SD cards.
3. Open the SD cards on your machine and edit your ```cmdline.txt``` file, add the following:
```cgroup_memory=1 cgroup_enable=memory ip=192.168.68.190::192.168.68.1:255.255.255.0:rpimaster:eth0:off```
where **ip=** is the IP address you are manually assigning to your pi, ```192.168.68.1``` is your routers IP address, 
```rpimaster``` is the name you are giving your Pi and ```eth0``` is your Pi's NIC. Remember to edit the IP addresses
and name to be unique per Pi (and make sure they are not already assigned on your network!).
***Note: ip=<client-ip>:<server-ip>:<gw-ip>:<netmask>:<hostname>:<device>:<autoconf>***
4. Open ```config.txt``` and add the following to the end of the file to make sure you use a 64 bit versions of Raspbian:
```
arm_64bit=1
```
5. Open the boot folder in your terminal and run:
```touch ssh```
This will enable ssh when you reboot the Pi.
6. Plug in the master Pi and ssh into it```ssh pi@192.168.68.190```, password = raspberry.
7. Become root ```sudo su -```
8. Install and enable iptables:
```
sudo apt-get install iptables
sudo iptables -F 
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy 
sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```
9. Reboot the Pi ```reboot```
10. Install K3s on the master node:
```
sudo su -
curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" sh -s -
```
Test it is working:
```
kubectl get nodes
```
11. Get the node token from the master node
```
sudo cat /var/lib/rancher/k3s/server/node-token
```
12. Install K3s on the other nodes:
```
curl -sfL https://get.k3s.io | K3S_TOKEN="YOURTOKEN" K3S_URL="https://[your server]:6443" K3S_NODE_NAME="servername" sh -
```
where ```K3S_TOKEN``` is your master token, ```K3S_URL``` is your master's IP address and ```K3S_NODE_NAME``` is the 
name you want to assign to your new node
13. Check everything is working from the master node:
```
kubectl get nodes
```