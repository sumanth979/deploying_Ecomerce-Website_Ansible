# deploying_Ecomerce-Website_Ansible

This is a sample e-commerce application built for learning purposes.
Here is the deployment Structure:
<img src="https://github.com/sumanth979/deploying_Ecomerce-Website_Ansible/blob/master/deployment%20structure.png" alt="Deployment">

Here's how to deploy it on CentOS systems:

## The code for ecommerce website is taken from here.
* https://github.com/kodekloudhub/learning-app-ecommerce

## Deploy Pre-Requisites

1. Install FirewallD

```
sudo yum install -y firewalld
sudo service firewalld start
sudo systemctl enable firewalld
```

## Deploy and Configure Database

1. Install MariaDB

```
sudo yum install -y mariadb-server
sudo service mariadb start
sudo systemctl enable mariadb
```

2. Configure firewall for Database

```
sudo firewall-cmd --permanent --zone=public --add-port=3306/tcp
sudo firewall-cmd --reload
```

## Deploy and Configure Web

1. Install required packages

```
sudo yum install -y httpd php php-mysql
sudo firewall-cmd --permanent --zone=public --add-port=80/tcp
sudo firewall-cmd --reload
```

2. Configure httpd

Change `DirectoryIndex index.html` to `DirectoryIndex index.php` to make the php page the default page

```
sudo sed -i 's/index.html/index.php/g' /etc/httpd/conf/httpd.conf
```

3. Start httpd

```
sudo service httpd start
sudo systemctl enable httpd
```

4. Download code

```
sudo yum install -y git
git clone https://github.com/kodekloudhub/learning-app-ecommerce.git /var/www/html/
```

5. Update index.php

Update index.php file to connect to the right database server. In this case `localhost` since the database is on the same server.

```
sudo sed -i 's/172.20.1.101/localhost/g' /var/www/html/index.php
```

6. Configure Database

```
$ mysql
MariaDB > CREATE DATABASE ecomdb;
MariaDB > CREATE USER 'ecomuser'@'localhost' IDENTIFIED BY 'ecompassword';
MariaDB > GRANT ALL PRIVILEGES ON *.* TO 'ecomuser'@'localhost';
MariaDB > FLUSH PRIVILEGES;
```

7. Load Product Inventory Information to database

Run sql script

```

mysql < /var/www/html/assets/db-load-script.sql
```

8. Test

```
curl http://localhost
```
Output:
![](https://github.com/sumanth979/deploying_Ecomerce-Website_Ansible/blob/master/deployment%20structure.png =250x250)
