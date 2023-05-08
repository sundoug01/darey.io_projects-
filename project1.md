## WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS -LAMP STACK 

A bundle of four distinct software technologies known as LAMP stack is utilized by developers to create websites and web applications. The term LAMP is an acronym for the four components of the stack, including Linux operating system, Apache web server, MySQL database server, and PHP programming language. All these technologies are open source, implying that they are maintained by the community and freely accessible for anyone to use.

## Linux
Steps involved

1. Launch an EC2 instance running Ubuntu server 22.04 LTS on AWS
   <img width="1224" alt="EC2 instance" src="https://user-images.githubusercontent.com/28840209/236896891-909fe473-f386-4dba-9c76-3b244a3ba2ff.png">
Note: Save the private key created (.pem file) securely as it will be used to connect to the server

2. Connecting to the EC2 instance 

 ![Connecting to EC2 instance](https://user-images.githubusercontent.com/28840209/236901459-7242a729-6c39-4a7d-a249-57fa60e2b035.png)  
- Note: ignore the *

<img width="441" alt="Connecting from terminal to the EC2" src="https://user-images.githubusercontent.com/28840209/236905662-c79ca8fa-c379-418c-a5f7-bc8ad3283ef6.png">

## Installing Apache

Install Apache using Ubuntu’s package manager ‘apt’:

Update a list of packages in package manager:
sudo apt update

Run apache2 package installation : 
sudo apt install apache2

To check status of the apache2 service, rub this command : sudo systemctl status apache2

<img width="562" alt="Status of the apache2 service" src="https://user-images.githubusercontent.com/28840209/236909045-962358aa-9610-4b28-8b96-f7692d866749.png">

Need to configure port 80 on the EC2 configuration to open inbound  connection through port 80

![Inbound rules configuration](https://user-images.githubusercontent.com/28840209/236911057-7f05e7f1-2c89-40df-9d05-28dc856841e8.png)

Access the webserver via the public IP assigned to the EC2 instance
![Apache web](https://user-images.githubusercontent.com/28840209/236911676-391f22e8-e04b-4925-9c14-6a2604258bd8.png)

## Installing Mysql

$ sudo apt install mysql-server

<img width="639" alt="Mysql status " src="https://user-images.githubusercontent.com/28840209/236914961-8b8d1bb4-24ee-4e42-8a91-27d19223868a.png">

To connect to the instance db, run *sudo mysql

## Installing PHP
I have Apache installed to serve the  content and MySQL installed to store and manage my data. PHP is the component of my setup that will process code to display dynamic content to the end user.In addition to the php package,php-mysql is required, a PHP module that allows PHP to communicate with MySQL-based databases.Also required is libapache2-mod-php to enable Apache to handle PHP files.

Run this command to install : *sudo apt install php libapache2-mod-php php-mysql

Verifying the version of the php installed

<img width="488" alt="PHP version check " src="https://user-images.githubusercontent.com/28840209/236919133-96f49c9e-9ddb-4123-b8e0-c717f430c579.png">

I have installed all the components of the LAMP stack, to test with a php script, I will be  setting up a virual host 

## Creating a virual host for my website using apache
-Setting up a domain called firstproject

Create the directory for firstproject: *sudo mkdir /var/www/firstproject*

Assign ownership of the directory to the current user : *sudo chown -R $USER:$USER /var/www/firstproject*

Create and open a new configuration file in Apache's sites-available directory : *sudo vi /etc/apache2/sites-available/firstproject.conf*

Add the configuration below to the file:

<img width="416" alt="Virtual host config " src="https://user-images.githubusercontent.com/28840209/236922397-cf9da683-236f-4d14-8db8-d1fd017aeb9a.png">

Save and close the file.

Use the ls command to show the new file in the sites-available directory : *sudo ls /etc/apache2/sites-available*

With the virtual host configuration, I am instructing apache to serve firstproject using /var/www/firstproject as its web root directory

Enabling the new virtual host using a2ensite : *sudo a2ensite projectlamp*

To disable Apache’s default website use a2dissite command , type: *sudo a2dissite 000-default*

To make sure your configuration file doesn’t contain syntax errors, run: *sudo apache2ctl configtest*

Finally, reload Apache so these changes take effect: *sudo systemctl reload apache2*

New website is ready but the web root is empty.

![Empty web root](https://user-images.githubusercontent.com/28840209/236928534-1b9a694f-5552-48c4-a184-7de29128b0ec.png)

I will create an index.html file  in the location 

sudo echo 'Hello LAMP from hostname' $(curl -s http://3.14.10.114/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://3.14.10.114/latest/meta-data/public-ipv4) > /var/www/firstproject/index.html

![updated the web root ](https://user-images.githubusercontent.com/28840209/236930295-4c402012-49fa-4d56-add3-acb7f87f8f8b.png)

## Enabling PHP on the Website 
With the default directoryindex settings on apache, index.html will always take precedence over an index.php file.  To change this, I will edit */etc/apache2/mods-enabled/dir.conf* and change the order in which the index.php file is listed

Run this command : *sudo vim /etc/apache2/mods-enabled/dir.conf*

<img width="534" alt="updated config file" src="https://user-images.githubusercontent.com/28840209/236932499-d6b506e1-9278-40ae-8e7a-413e1867ba92.png">

After saving and closing the file, you will need to reload Apache so the changes take effect: *sudo systemctl reload apache2*

Finally, I am creating a PHP script to test and confirm that PHP is correctly installed and configured on my server

Creating a new file named index.php inside my custom web root folder: *sudo vim /var/www/firstproject/index.php*

Add code below, save and reload the page

*<?php
phpinfo();*

![Enabled PHP](https://user-images.githubusercontent.com/28840209/236934470-782381fd-8c17-40cc-b4ec-c14400ede9bf.png)
