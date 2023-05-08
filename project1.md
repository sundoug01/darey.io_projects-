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

