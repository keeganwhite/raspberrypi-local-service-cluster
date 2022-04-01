# Raspberry Pi Local Services Cluster
## Overview
## Equipment
## Preparation (headless)
1. Flash your SD cards with Raspbian Pi OS Lite (32-bit)
2. Insert the SD cards into the Pis and boot them. Let them boot (wait a few minutes). Turn off the Pis and remove the
SD cards.
3. Open the SD cards on your machine and edit your ```cmdline.txt``` file, so it reads as follows:
```console=serial0,115200 console=tty1 root=PARTUUID=e62ded4d-02 rootfstype=ext4 fsck.repair=yes rootwait cgroup_memory=1 cgroup_enable=memory ip=192.168.68.190::192.168.68.1:255.255.255.0:rpimaster:eth0:off```
where **ip=** is the IP address you are manually assigning to your pi, ```192.168.68.1``` is your routers IP address, 
```rpimaster``` is the name you are giving your Pi and ```eth0``` is your Pi's NIC. Remember to edit the IP addresses
and name to be unique per Pi (and make sure they are not already assigned on your network!).
***Note: ip=<client-ip>:<server-ip>:<gw-ip>:<netmask>:<hostname>:<device>:<autoconf>***
4. Open ```config.txt``` and add the following to the end of the file to make sure you use a 64 bit versions of Raspbian:
5. Open the boot folder in your terminal and run:
```touch ssh```
This will enable ssh when you reboot the Pi.
6. ssh into the pi ```ssh pi@192.168.68.190```, password = raspberry.