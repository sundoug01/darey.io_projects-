


# Step 1 — Prepare a Web Server

- Launch an EC2 instance that will serve as "Web Server".


<img width="997" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/ef1d1038-6e07-4ecc-a632-f6dffef6b702">


- Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB


<img width="628" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/aa728341-790f-49ac-865e-d2d4b1735b89">


- Attach all three volumes one by one to your Web Server EC2 instance


<img width="1127" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/9c364255-78fa-423e-807e-a3ab0c028212">



<img width="844" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/2ee7e441-a601-4e6c-bf19-1902c92d97a7">



<img width="1131" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/775056dc-639e-4d75-a8a3-0ebaa8d45906">


> Use ``` lsblk ``` command to inspect what block devices are attached to the server, list  the /dev/ directory to confirm the attached volumes  and use the ```df -h ```
>  to see all mounts and free space on the web-server


<img width="998" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/aac46b19-f441-4531-93a3-7c6c4f6ab6b8">

- Use ``` gdisk ``` utility to create a single partition on each of the 3 disks
  
```
sudo gdisk /dev/xvdf  sudo gdisk /dev/xvdg  sudo gdisk /dev/xvdh
```
<img width="626" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/cc54c52a-09ee-4ce9-b364-310837a34663">


- Use ``` lsblk ``` utility to view the newly configured partition on each of the 3 disks.

  <img width="349" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/4e922e76-129c-483e-bf09-545d62e0fec8">


- Install lvm2 package using ``` sudo yum install lvm2 ``` . Run  ``` sudo lvmdiskscan ``` command to check for available partitions.

> Had to register the vm and also ensure the vm has internet connection by editing the outbound rules

 <img width="357" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/2571c907-2692-47d6-9bb8-7a4cf5c6e2f0">


- Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM
```
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1

```
- Verify that your Physical volume has been created successfully by running ``` sudo pvs ```

<img width="372" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/43b8d728-a92c-4f21-8968-ca0baacc049f">


- Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg
```
sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
```
- Verify that your VG has been created successfully by running ``` sudo vgs ``` 

<img width="452" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/829654a5-3ea6-4d44-8cee-596470e20e61">



- Use ``` lvcreate ``` utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size.
> NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.
```
sudo lvcreate -n apps-lv -L 14G webdata-vg
```
```
sudo lvcreate -n logs-lv -L 14G webdata-vg
```
- Verify that your Logical Volume has been created successfully by running ``` sudo lvs ```

<img width="736" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/76305ccb-1c12-4047-a834-dd8f20753d87">


- Verify the entire setup
```
sudo vgdisplay -v

```
#view complete setup - VG, PV, and LV
```
sudo lsblk
```

<img width="524" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/3d4bcf2d-7845-432b-a1ea-176e69b05e62">


- Use mkfs.ext4 to format the logical volumes with ext4 filesystem
```
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
```
```
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```
- Create /var/www/html directory to store website files
```
sudo mkdir -p /var/www/html
```
- Create /home/recovery/logs to store backup of log data
```
sudo mkdir -p /home/recovery/logs
```
- Mount /var/www/html on apps-lv logical volume
```
sudo mount /dev/webdata-vg/apps-lv /var/www/html/
```
- Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)
```
sudo rsync -av /var/log/. /home/recovery/logs/
```
- Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above is very
important)
```
sudo mount /dev/webdata-vg/logs-lv /var/log
```
- Restore log files back into /var/log directory
```
sudo rsync -av /home/recovery/logs/. /var/log
```

- Update /etc/fstab file so that the mount configuration will persist after restart of the server

> The UUID of the device will be used to update the /etc/fstab file;
```
sudo blkid
```

```
sudo vi /etc/fstab
```
Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes.



- Test the configuration and reload the daemon
```
 sudo mount -a
 sudo systemctl daemon-reload
```
> Verify your setup by running ``` df -h ``` , output must look like this:


# Step 2 — Prepare the Database Server

- Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
- Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.

# Step 3 — Install WordPress on your Web Server EC2
- Update the repository
```
sudo yum -y update
```
- Install wget, Apache and it’s dependencies
```
sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json
```
- Start Apache
```
sudo systemctl enable httpd
sudo systemctl start httpd
```
<img width="832" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/589aff30-29f4-44a5-8b58-33d0366b0a86">


- To install PHP and it’s depemdencies
```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
sudo setsebool -P httpd_execmem 1
```
- Restart Apache
```
sudo systemctl restart httpd
```
- Download wordpress and copy wordpress to var/www/html
```
  mkdir wordpress
  cd   wordpress
  sudo wget http://wordpress.org/latest.tar.gz
  sudo tar xzvf latest.tar.gz
  sudo rm -rf latest.tar.gz
  sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
  sudo cp -R wordpress /var/www/html/
```

- Configure SELinux Policies
```
  sudo chown -R apache:apache /var/www/html/wordpress
  sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
  sudo setsebool -P httpd_can_network_connect=1
```
# Step 4 — Install MySQL on your DB Server EC2

