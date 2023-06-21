## WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

What is a Technology stack?
A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. They are acronymns for individual technologies used together for a specific technology product. some examples are…

-LAMP (Linux, Apache, MySQL, PHP or Python, or Perl).
-LEMP (Linux, Nginx, MySQL, PHP or Python, or Perl).
-MERN (MongoDB, ExpressJS, ReactJS, NodeJS).
-MEAN (MongoDB, ExpressJS, AngularJS, NodeJS.

### BELOW ARE THE STEPS TAKEN TO DEPLOY THE LAMP STACK ON AWS.

#### Step 0 – Preparing prerequisites:
I set up an EC2 instance using Ubuntu 22.04 AMI AND T2.Micro as instance type and connected to the Ubuntu server via ssh.

![Screenshot from 2023-06-21 17-03-31](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/7e293383-c6be-493f-8356-920087a1cbd8)

#### STEP 1 — INSTALLING APACHE AND UPDATING THE FIREWALL:
* I updated a list of packages in package manager uisng: sudo apt update
* I installed apache2 using : sudo apt install apache2
* verified that apache2 is running as a service using: sudo systemctl status apache2 (below is a screenshot of confirmation)
![Screenshot from 2023-06-21 17-21-53](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/a6f1f294-7058-4215-bdef-4d30c48be0f7)
* To verify via the web that apache2 was succesfully installed, i opened up a browser and tried accessing via url
![Screenshot from 2023-06-21 17-30-57](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/29e62119-f2f6-4af3-ae70-ae8a5a684f9c)

#### STEP 2 — INSTALLING MYSQL:
* i installed mySql using: sudo apt install mysql-server
* I logged into the MySQL console using: sudo mysql
* I ran a security script that comes pre-installed with MySQL i.e " sudo mysql_secure_installation ". This script will remove some insecure default settings and lock down access to your database system. Before running the script I set a password for the root user, using mysql_native_password as default authentication method.
* I tested that mySQL would work only with given password.

#### STEP 3 — INSTALLING PHP:
*I installed the php package, alongside php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases, and libapache2-mod-php, a module that enables Apache to handle PHP files, using the following command: sudo apt install php libapache2-mod-php php-mysql.
*To confirm succesful installation of the php modeles i ran the command: php -v, and i got this in return:
![Screenshot from 2023-06-21 18-15-24](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/3d1b651b-f076-46a5-a927-3b92c4eef623)
confirming that i have php up and running.

#### STEP 4 — CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE:
* I set up a domain called projectlamp using the command: sudo mkdir /var/www/projectlamp
* I then assigned ownership of the created directory to the current system user with the command:  sudo chown -R $USER:$USER /var/www/projectlamp
* Then, I created and opend a new configuration file in Apache’s sites-available directory using vim editor:
sudo vi /etc/apache2/sites-available/projectlamp.conf
* Then, I inputed the following:
![Screenshot from 2023-06-21 18-40-31](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/f2a48cf5-d69b-46e3-a019-4917b950849f)

With this VirtualHost configuration, I told Apache to serve projectlamp using /var/www/projectlamp as its web root directory. 
* Then, I used a2ensite command to enable the new virtual host: sudo a2ensite projectlamp 
* Then, I disabled the default website that comes installed with Apache. This is required if you’re not using a custom domain name, because in this case Apache’s default configuration would overwrite your virtual host. I disabled Apache’s default website using the following command:
sudo a2dissite 000-default
* Then, I ran the following command to ensure my configuration does not have syntax errors: sudo apache2ctl configtest.
* Then, I reloaded Apache so these changes take effect using: sudo systemctl reload apache2
* Then, I created an index.html file in that location so that I can test that the virtual host works as expected:
sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
* Then, I went back to the already opened browser and refreshed:
![Screenshot from 2023-06-21 19-01-48](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/27f71558-4eb1-48a8-945d-b134b151ef00)

#### STEP 5 — ENABLE PHP ON THE WEBSITE:
* I edited the dir.conf file to make index.php take precedence over index.html:
![Screenshot from 2023-06-21 19-40-20](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/39845a3e-47d0-49c5-b50a-382d2b5c587d)
* Then i reloaded the Apache server using: sudo systemctl reload apache2
* Then i created an index.php file inside the projectlamp directory using: vim /var/www/projectlamp/index.php
* Then i inserted the following into the text editor: <?php
phpinfo();
* Then, I went back to the already opened browser and refreshed:
![Screenshot from 2023-06-21 19-47-45](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/b9c4a906-a086-4c0d-aac0-80b690e855f3)
* Then I removed the just created index.php file, as it contains sensitive information about my PHP environment -and your Ubuntu server:
sudo rm /var/www/projectlamp/index.php
