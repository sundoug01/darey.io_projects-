## LEMP STACK IMPLEMENTATION
This consists of Linux, Nginx, MySQL and PHP

As it was done in project1 https://github.com/sundoug01/darey.io_projects-/blob/main/project1.md , launch an EC2 instance,
update the packages and install Nginx by running this commands respectively 

*sudo apt install nginx*
- Check status by running *sudo stystemctl status nginx*
![Nginx installed ](https://github.com/sundoug01/darey.io_projects-/assets/28840209/3a326ec4-cf26-4bfc-af60-d4ec450bd529)

## Installing MySQL and PHP
As done in project1, difference is installation of php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing
- *sudo apt install php-fpm php-mysql*

## Configuring Nginx to Use PHP Processor


## Testing PHP with Nginx

![Testing  PHP with Nginx](https://github.com/sundoug01/darey.io_projects-/assets/28840209/d2c9d303-6855-4287-818e-e9209cdc0452)

##  Retrieving data from MySQL database with PHP

![Retrieving data from database ](https://github.com/sundoug01/darey.io_projects-/assets/28840209/e26bd833-5b25-417b-a0d9-71890a6999dd)




