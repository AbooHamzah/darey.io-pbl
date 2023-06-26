## WEB STACK IMPLEMENTATION (LEMP STACK)
### What is a Technology stack?
A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. They are acronymns for individual technologies used together for a specific technology product. some examples are…

* LAMP (Linux, Apache, MySQL, PHP or Python, or Perl)
* LEMP (Linux, Nginx, MySQL, PHP or Python, or Perl)
* MERN (MongoDB, ExpressJS, ReactJS, NodeJS)
* MEAN (MongoDB, ExpressJS, AngularJS, NodeJS

### BELOW ARE THE STEPS TAKEN TO DEPLOY THE LEMP STACK ON AWS.
#### Step 0 – PREPARING PREREQUISITES:
* I set up an EC2 instance using Ubuntu 22.04 AMI AND T2.Micro as instance type and connected to the Ubuntu server via ssh.

![Screenshot from 2023-06-21 17-03-31](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/7e293383-c6be-493f-8356-920087a1cbd8)

#### Step 1 – INSTALLING THE NGINX WEB SERVER:
* I updated a list of packages in package manager uisng: sudo apt update.
* Then, I installed the nginx server using: sudo apt install nginx.
* Then, I verified that the installed nginx server was active, using: sudo systemctl status nginx.
  ![Screenshot from 2023-06-26 11-12-47](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/73a6fd0b-4e42-4cf2-8fb0-7704c306a53c)
  
* Then, I verified that i could reach the nginx server over the web:
  ![Screenshot from 2023-06-26 11-20-16](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/edfb13dd-f3ca-452c-9900-c400cf155fad)

#### STEP 2 — INSTALLING MYSQL: 
After getting the web server up and running, I needed to install a Database Management System (DBMS) to be able to store and manage data for your site in a relational database. MySQL is a popular relational database management system used within PHP environments, so we will use it in our project.

* I installed the mySQL server using: sudo apt install mysql-server.
* Then I, verified if it had been installed and entered into the mysql console using: sudo mysql.
  ![Screenshot from 2023-06-26 11-34-17](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/592b16c4-183d-4d51-832b-63030d5c01eb)
  
* Then, I set a password for the mysql root user, using mysql_native_password as default authentication method: ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
* Then, I exited the mysql console so that the effected passwird change can take effect.
* Then, I ran a security script that comes pre-installed with MySQL. This script is intended to remove some insecure default settings and lock down access to the database system: sudo mysql_secure_installation.
*  Then, I setup VALIDATE PASSWORD component.
  ![Screenshot from 2023-06-26 11-52-04](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/0a233218-222e-484e-bfbf-06cee76b6b23)

*  Then, I set the strength of the password validation.
  ![Screenshot from 2023-06-26 11-52-57](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/0c0a0698-45be-42a2-a300-c97406edfdf8)

*  Then, I changed my password to match chosen strength, removed anonymous user and disallowed remote root login, to ensure extra security.
  
  ![Screenshot from 2023-06-26 11-53-52](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/1a4229c1-2f73-4f1f-9d74-25be1981f172)

* Then, I tested if I would be able to log in to the MySQL console by typing: sudo mysql -p.
* After having confirmed that i can log into the installed mysql server using the set password, I exit using: exit.

  #### STEP 3 – INSTALLING PHP:
After having installed NGINX to serve your content and MySQL to store and manage my data. I then proceeded to installing PHP to process code and generate dynamic content for the web server.

* I needed to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, I needed php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages were automatically be installed as dependencies: sudo apt install php-fpm php-mysql

  
  #### STEP 4 — CONFIGURING NGINX TO USE PHP PROCESSOR:
When using the Nginx web server, we can create multiple server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. We will name our domain, projectLEMP.
On Ubuntu 22.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html. While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. Instead of modifying /var/www/html, we’ll create a directory structure within /var/www for the your_domain website, leaving /var/www/html in place as the default directory to be served if a client request does not match any other sites.
* I created a root directory for my projectLEMP domain by typing the following: sudo mkdir /var/www/projectLEMP
* Then, I assign ownership of the directory with the $USER environment variable, which automatically referenced my current system user: sudo chown -R $USER:$USER /var/www/projectLEMP
* Then, I open a new configuration file in Nginx’s sites-available directory using vim: sudo vim /etc/nginx/sites-available/projectLEMP
  ![Screenshot from 2023-06-26 12-24-45](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/aaedbe13-f039-45d0-ac52-565a3763bbda)
  
 ##### Here’s what each of these directives and location blocks do:
![Screenshot from 2023-06-26 12-37-03](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/9afb23c5-9a5c-4fa0-8699-4d347b9e32e8)

* Then, I activated the configuration by linking to the config file from Nginx’s sites-enabled directory, this will tell Nginx to use the configuration next time it is reloaded by typing: sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
* Then, I tested my configuration for syntax errors using: sudo nginx -t
* Then, I disabled the default Nginx host that was initially configured to listen on port 80, using: sudo unlink /etc/nginx/sites-enabled/default
* Then, I reloaded Nginx to apply the changes, using: sudo systemctl reload nginx
* Then, I created an index.html file in that location so that I could test that the new server block worked as expected:
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
* Then, I went back to my browser to the page I used to test my NGINX server and i refreshed it(ip address):
  ![Screenshot from 2023-06-26 12-51-38](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/d2f000f8-9ea2-4e7c-9307-421e58cc621d)
  
* Seeing the content of the index.html file under my projectLEMP domain certifies that the NGINX site works as intended.

  #### STEP 5 – TESTING PHP WITH NGINX:
  At this point, the LEMP stack is completely installed and fully operational. I then tested to validate that Nginx could correctly handle .php files off to the PHP processor.
* I opened a new file called info.php within your document root in the text editor: sudo vim /var/www/projectLEMP/info.php
* Then, I typed the following php codes:<?php
phpinfo();
* Then, I verified that I could view the php content by going to a web browser and inputing the following: http://`server_domain_or_IP`/info.php.
  ![Screenshot from 2023-06-26 13-08-05](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/e4a9ab9f-84e0-4ce4-abfa-abd8e7574ee6)

* Then, I removed the info.php file from the domain projectLEMP because it's content is not suppose to be out on the web for security reasons: sudo rm /var/www/projectLEMP/info.php

#### STEP 6 – RETRIEVING DATA FROM MYSQL DATABASE WITH PHP:
In this step I created a test database (DB) with a simple “To do list” and configured access to it, so the Nginx website would be able to query data from the DB and display it.
* I entered into the mysql server using: sudo mysql -p, and then inputed the configured password.
* Then, I created a new database using: CREATE DATABASE `example_database`; .
* Then, I created a user and a password that matched the set password policy: CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
* Then, I granted the just created user 'example_user' permission over 'example_database' using: GRANT ALL ON example_database.* TO 'example_user'@'%';
* Then, I exit the console so that the modified configurations would take effect using: exit.
* Then, I tested if the new user had the proper permissions by logging in to the MySQL console again, this time using the custom user credentials: mysql -u example_user -p
* After I logged into the MySQL console, I confirmed that I had access to the example_database database: SHOW DATABASES;
![Screenshot from 2023-06-26 13-27-49](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/2430b589-4dd9-43b7-8b74-6464fce88b16)

* Then I created a test table named todo_list. From the MySQL console, using: CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));
* Then, I Inserted a few rows of content in the test table. I repeated the next command a few times, using different VALUES: INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
* Then I confirmed that the data was successfully saved to the table,by running: SELECT * FROM example_database.todo_list;
  ![Screenshot from 2023-06-26 13-35-26](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/7531b90b-d469-46b0-a0fa-de44d1e0af57)
  
* Then, I exited the mysql console: exit.
* Then, I created a PHP script that connected to MySQL and queried for your content. The new PHP file was created in the custom web root directory using vim: vim /var/www/projectLEMP/todo_list.php
* Then, the content below was filled into the editor:
  ![Screenshot from 2023-06-26 14-35-52](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/2b37deba-3b08-4c34-9e9d-f1b8b0ee7214)
  
* Then, I confirmed that the PHP environment was ready to connect and interact with the MySQL server, by accessing through the web the domain name or ip address, followed by /todo_list.php: http://<Public_domain_or_IP>/todo_list.php
  ![Screenshot from 2023-06-26 14-38-25](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/74451679-9505-4272-ad1f-53fa5a362ece)


