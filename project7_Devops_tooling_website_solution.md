# Implementing a tooling website solution 
## Step 1 – Prepare NFS Server
Spin up a new EC2 instance with RHEL Linux 9 Operating System.

Based on the LVM experience from Project 6, Configure LVM on the Server.

> Instead of formating the disks as ext4 you will have to format them as xfs

Ensure there are 3 Logical Volumes. lv-opt lv-apps, and lv-logs

- Create mount points on /mnt directory for the logical volumes as follow:
Mount lv-apps on /mnt/apps – To be used by webservers
Mount lv-logs on /mnt/logs – To be used by webserver logs
Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8

<img width="542" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/ce60b03b-3d0b-4dce-b478-4cc8b209b535">


- Install NFS server, configure it to start on reboot and make sure it is up and running
```
sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service

```
Export the mounts for webservers’ subnet cidr to connect as clients.To check subnet cidr – open your EC2 details in AWS web console and locate ‘Networking’ tab and open a Subnet link


- Set up permission that will allow our Web servers to read, write and execute files on NFS-Server
```
sudo chown -R nobody: /mnt/apps
sudo chown -R nobody: /mnt/logs
sudo chown -R nobody: /mnt/opt
```
```
sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt
```
```
sudo systemctl restart nfs-server.service
```
- Configure access to NFS for clients within the same subnet (example of Subnet CIDR – 172.31.32.0/20 ):

<img width="1177" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/97c9c508-ffab-4d2f-b5bc-e8d4dba6bf9b">

<img width="1096" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/69b70b8f-1f52-4c8d-9d2d-8e114975b3b9">


```
sudo vi /etc/exports
```

/mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)


```
sudo exportfs -arv
```
Check which port is used by NFS and open it using Security Groups (add new Inbound Rule)
```
rpcinfo -p | grep nfs
```

Important note: In order for NFS server to be accessible from your client, you must also open following ports: TCP 111, UDP 111, UDP 2049

# STEP 2 — Configure the database server

[Install MySQL server ](https://linuxhint.com/install-mysql-on-ubuntu-22-04/)

Create a database and name it tooling
```
create database tooling;
```
Create a database user and name it webaccess
```
create user 'webaccess'@'webserver-subnet-id' identified by 'YourPasswordHere'
```
Grant permission to webaccess user on tooling database to do anything only from the webservers subnet cidr

```
grant all privileges on tooling.* to 'webaccess'@subnet';
```

# Step 3 — Prepare the Web Servers
We need to make sure that our Web Servers can serve the same content from shared storage solutions, in our case – NFS Server and MySQL database.
You already know that one DB can be accessed for reads and writes by multiple clients. For storing shared files that our Web Servers will use – we will utilize NFS and mount previously created Logical Volume lv-apps to the folder where Apache stores files to be served to the users (/var/www).
>
Configure NFS client (this step must be done on all three servers)
Deploy a Tooling application to our Web Servers into a shared NFS folder
Configure the Web Servers to work with a single MySQL database
Launch a new EC2 instance with RHEL 8 Operating System
>
-Install NFS client
```
sudo yum install nfs-utils nfs4-acl-tools -y
```
- Mount /var/www/ and target the NFS server’s export for apps
```
sudo mkdir /var/www
```

```
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
```
-Verify that NFS was mounted successfully by running ``` df -h ```. Make sure that the changes will persist on Web Server after reboot:

<img width="1158" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/45520562-0535-4fae-860e-eb759884b451">

```
sudo vi /etc/fstab
```
add following line
```
<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0
```

-Install Remi’s repository, Apache and PHP
```
sudo yum install httpd -y

sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

sudo dnf module reset php

sudo dnf module enable php

sudo dnf install php php-opcache php-gd php-curl php-mysqlnd

sudo systemctl start php-fpm

sudo systemctl enable php-fpm

sudo setsebool -P httpd_execmem 1
```

**Repeat steps 1-5 for another 2 Web Servers.

Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps. If you see the same files – it means NFS is mounted correctly. You can try to create a new file touch test.txt from one server and check if the same file is accessible from other Web Servers.

<img width="582" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/bda127fb-befb-4c4c-925c-03dd5257982c">

<img width="471" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/4a45ba0e-0045-42a3-b18e-bc42201ecf5a">



Locate the log folder for Apache on the Web Server and mount it to NFS server’s export for logs. Repeat step №4 to make sure the mount point will persist after reboot.
```
<NFS-Server-Private-IP>:/mnt/logs /var/log/httpd nfs defaults 0 0
```
Fork the tooling source code from Darey.io Github Account to your Github account.

Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html

<img width="565" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/e23f8f6b-7d96-4c27-9e7e-367b28de44b6">

Modify the bind address and restart the service 

```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
sudo systemctl restart mysql.service
```

-Fork the tooling source code from Darey.io Github Account to your Github account. (Learn how to fork a repo here)

-Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html

Note 1: Do not forget to open TCP port 80 on the Web Server.

Note 2: If you encounter 403 Error – check permissions to your /var/www/html folder and also disable SELinux sudo setenforce 0
To make this change permanent – open following config file ```  sudo vi /etc/sysconfig/selinux ``` and ``` set SELINUX=disabled ``` then restart httpd.



Update the website’s configuration to connect to the database (in /var/www/html/functions.php file). Apply tooling-db.sql script to your database using this command ``` mysql -h <databse-private-ip> -u <db-username> -p <db-pasword> < tooling-db.sql ```

<img width="569" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/19012cee-aaa9-4773-bfe6-483bdc1fa789">


<img width="580" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/eafae1a2-1c26-47fe-a48c-e8974230cbda">

-Create in MySQL a new admin user with username: myuser and password: password:

INSERT INTO ‘users’ (‘id’, ‘username’, ‘password’, ’email’, ‘user_type’, ‘status’) VALUES
-> (1, ‘myuser’, ‘5f4dcc3b5aa765d61d8327deb882cf99’, ‘user@mail.com’, ‘admin’, ‘1’);

Open the website in your browser http://<Web-Server-Public-IP-Address-or-Public-DNS-Name>/index.php and make sure you can login into the websute with myuser user.



mysql -h 172.31.1.92 -u webaccess -p tooling < tooling-db.sql
