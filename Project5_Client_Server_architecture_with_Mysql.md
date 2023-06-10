## Step 1
- Creating and configuring two Linux-based virtual servers (EC2  instances in AWS)


![EC2 instances launched](https://github.com/sundoug01/darey.io_projects-/assets/28840209/e9b209b3-eb0e-4b9b-8a55-0b84bab86e6b)

## Step 2
- Install mysql server on ServerB-server 
  [install mysql](https://ubuntu.com/server/docs/databases-mysql)
  
  - check the status of the mysql 
  <img width="643" alt="Mysql status on Server" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/79477c6e-9a08-40f6-9742-7ba5ae0de2fa">

## Step 3 
- Install Mysql client on ServerA-client
  [install mysql](https://ubuntu.com/server/docs/databases-mysql)
  
## Step 4
- Configure Mysql server to allow connection from remote hosts 
```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```
Replace '172.0.0.1' to '0.0.0.0' as shown below 

<img width="598" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/d8aa342e-4d0b-4634-a673-31c26cb59fd6">

## Step 5
- Using the mysql utility to connect remotely the mysql server from mysql client 

i. Create a new entry in inbound rules in the ServerB-server security group

![image](https://github.com/sundoug01/darey.io_projects-/assets/28840209/ce95a502-0db6-49d1-b41e-47eb81faa2d6)

ii. Initiate connection to mysql server from mysql server client, username=

```
mysql -u 'username' -h 'mysql_server_ip' -p
```
<img width="552" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/3a211c92-2d09-43c9-8f79-e59e45f28449">

> Note: You need the create the user you will use to connect from the client server in the server mysql and grant priviledge 

<img width="571" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/6b9712fb-a98d-485d-a7a3-0d1009c92cdf">


> Helpful resources and errors encountered
-  [remote connection mysql](https://phoenixnap.com/kb/mysql-remote-connection)
-  [Host is not allowed ](https://stackoverflow.com/questions/19101243/error-1130-hy000-host-is-not-allowed-to-connect-to-this-mysql-server)
-  [Failed! Error:PASSWORD](https://www.nixcraft.com/t/mysql-failed-error-set-password-has-no-significance-for-user-root-localhost-as-the-authentication-method-used-doesnt-store-authentication-data-in-the-mysql-server-please-consider-using-alter-user/4233)
