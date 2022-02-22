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

creating physical volumes for each disk

` $ sudo pvcreate /dev/xvdf1`

` $ sudo pvcreate /dev/xvdg1`

` $ sudo pvcreate /dev/xvdh1`

verifying that physical volumes were created 

` $ sudo pvs`

creating volume group and adding all 3 physical volumes to the volume group

` $ sudo vgcreate webdata-vg /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

confirming that volume group was created and physical volumes was added

` $ sudo vgs`

creating 2 logical volumes to store website data and logs

` $ sudo lvcreate -n apps-lv -L 14G webdata-vg`

` $ sudo lvcreate -n logs-lv -L 14G webdata-vg`

verifying that logical volumes were created

` $ sudo lvs`

![](images/pvvglv11.png)

verifying the entire setup

` $ sudo vgdisplay -v`

![](images/vgdisplay12.png)

formatting the logical volumes with ext4 filesystem

` $ sudo mkfs -t ext4 /dev/webdata-vg/apps-lv`

` $ sudo mkfs -t ext4 /dev/webdata-vg/logs-lv`

![](images/mkfs13.png)

creating /html directory to store website files

` $ sudo mkdir -p /var/www/html`

creating /logs directory to store backup of log data

` $ sudo mkdir -p /home/recovery/logs`

mounting /html directory on apps-lv logical volume

` $ sudo mount /dev/webdata-vg/apps-lv /var/www/html/`

using rsync to backup all files in the /var/log directory before mounting into /home/recovery/logs

` $ sudo rsync -av /var/log/. /home/recovery/logs/`

` $ sudo mount /dev/webdata-vg/logs-lv /var/log`

restoring log files back into /var/log directory

` $ sudo rsync -av /home/recovery/logs/. /var/log`

![](images/dirbkup14.png)
![](images/dirbkup14again.png)

the /etc/fstab file will be updated with the uuid of the different devices

` $ sudo blkid`

` $ sudo vi /etc/fstab`

![](images/blkidetc15.png)

![](images/fstab16.png)

testing the configuration and reloading the daemon

` $ sudo mount -a`

` $ sudo systemctl daemon-reload`

verifying the setup 

` $ df -h`

![](images/mountdaemon17.png)

### repeating all steps above for the database server

the difference is that the  db has db-lv logical volume and it's mounted to /db directory

![](images/update.png)

![](images/dbvol.png)

![](images/db1.png)

![](images/dbxvdf2.png)

![](images/dbxvdg3.png)

![](images/dbxvdh4.png)

![](images/dblsblk5.png)

![](images/dblvminst6.png)

![](images/dblvmdlvcreate7.png)

![](images/dbverify8.png)

![](images/dbmkfs2fstab9.png)

![](images/dbfstabveri10.png)

### installing wordpress on web server

installing wget, apache and its dependencies

` $ sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`

starting apache 

` $ sudo systemctl enable httpd`

` $ sudo systemctl start httpd`

including some php dependencies 

` $ sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

` $ sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`

` $ sudo yum module list php`

` $ sudo yum module reset php`

` $ sudo yum module enable php:remi-8.1`

` $ sudo yum install php php-opcache php-gd php-curl php-mysqlnd`

` $ sudo systemctl start php-fpm`

` $ sudo systemctl enable php-fpm`

` $ setsebool -P httpd_execmem 1`

restarting apache 

` $ sudo systemctl restart httpd`

downloading wordpress and copying wordpress to /html

` $ mkdir wordpress && cd wordpress`

` $ sudo wget http://wordpress.org/latest.tar.gz`

` $ sudo tar xzvf latest.tar.gz`

` $ sudo rm -rf latest.tar.gz`

` $ cp wordpress/wp-config-sample.php wp-config.php`

` $ cp -R /var/www/html/`

configuring selinux policies

` $ sudo chown -R apache:apache /var/www/html/`

` $ sudo chcon -t httpd_sys_rw_content_t /var/www/html -R`

` $ sudo setsebool -P httpd_can_network_connect=1`

![](images/wpinst1.png)

![](images/wphpstatus2.png)

![](images/wphttpd3.png)

![](images/wpmkdir2xtract4.png)

![](images/wpcontent5.png)

![](images/wpcpcre6.png)

![](images/wphome2varhtml7.png)

