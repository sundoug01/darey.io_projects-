
# Deploy and configure an Apache Load Balancer for Tooling Website solution from project 7 


- Configure Apache As A Load Balancer

Create an Ubuntu Server 20.04 EC2 instance and name it Project-8-apache-lb, so your EC2 list will look like this:

![image](https://github.com/sundoug01/darey.io_projects-/assets/28840209/f454f280-f81a-40bd-930c-dd44b68795b5)


Open TCP port 80 on Project-8-apache-lb by creating an Inbound Rule in Security Group.

![image](https://github.com/sundoug01/darey.io_projects-/assets/28840209/f5c95f4c-a22c-4571-a737-e25f27099447)


Install Apache Load Balancer on Project-8-apache-lb server and configure it to point traffic coming to LB to both Web Servers:

- Install apache2
```
sudo apt update
sudo apt install apache2 -y
sudo apt-get install libxml2-dev

```


- Enable following modules:
```

sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic

```

- Restart apache2 service
```
sudo systemctl restart apache2
```
Make sure apache2 is up and running
```
sudo systemctl status apache2
```
- Configure load balancing
```
sudo vi /etc/apache2/sites-available/000-default.conf
```
- Add this configuration into this section <VirtualHost *:80>  </VirtualHost>

```
<Proxy "balancer://mycluster">
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/
```

<img width="1103" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/8eac0764-c3fb-4ffb-adf3-4d941190e576">



<img width="747" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/b21ba263-ce02-45ea-a2f6-b153a4709ada">


- Restart apache server
```
sudo systemctl restart apache2
```
bytraffic balancing method will distribute incoming load between your Web Servers according to current traffic load. We can control in which proportion the traffic must be distributed by loadfactor parameter.

You can also study and try other methods, like: bybusyness, byrequests, heartbeat

Verify that our configuration works – try to access your LB’s public IP address or Public DNS name from your browser:
```
http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php
```

![image](https://github.com/sundoug01/darey.io_projects-/assets/28840209/ff393347-9c2e-493a-b7e6-20811c3f349f)

![image](https://github.com/sundoug01/darey.io_projects-/assets/28840209/b3c47cb0-c730-4086-8eb6-f85cf01293ba)

