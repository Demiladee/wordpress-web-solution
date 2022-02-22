# Project 6

Launching an EC2 Instance that will serve as a Web Server and attaching three 10G volumes to it

___
![](images/vol1.png)

updating red hat

` $ sudo yum update -y`

![](images/update.png)

inspecting the block devices attached to the server

` $ lsblk`

confirming how much space i have on the disks

` $ df -h`

![](images/devdf4.png)

creating single partitions on each of the 3 disks

` $ sudo gdisk /dev/xvdf`

` $ sudo gdisk /dev/xvdg`

` $ sudo gdisk /dev/xvdh`

![](images/xdvf5.png)
![](images/xvdf5again.png)

![](images/xvdg6.png)

![](images/xvdh7.png)

viewing the newly configured partition on the disks

` $ lsblk`

![](images/lsblk8.png)

installing lvm2 package and checking for available partitions

` $ sudo yum install lvm2 -y`

` $ sudo lvmdiskscan`

![](images/yuminst9.png)

![](images/lvmdiskscan10.png)

