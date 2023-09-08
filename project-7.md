## DEVOPS TOOLING WEBSITE SOLUTION

### I implemented a tooling website solution which makes access to DevOps tools within the corporate infrastructure easily accessible. This solution consists of following components:

1) Infrastructure: AWS
2) Webserver Linux: Red Hat Enterprise Linux 8
3) Database Server: Ubuntu 20.04 + MySQL
4) Storage Server: Red Hat Enterprise Linux 8 + NFS Server
5) Programming Language: PHP
6) Code Repository: GitHub

### I utilized the 3-tier Web Application Architecture with a single Database and NFS server as a shared file storage:
  
![Tooling-Website-Infrastructure](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/79574931-bebb-4fad-980f-ed120e522a1d)


### STEP 1- PREPARING THE NFS SERVER:

* I launched an EC2 instance which I named NFS SERVER, i chose a RHEL 9 AMI and a t2 micro as instance type.
* Then, I added 3 more EBS volumes each of 10GB to give enough space for the logical volumes I intended to create.

![Screenshot from 2023-08-03 10-29-10](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/46b37622-d8f5-454b-9f3a-cddf14f9cfdd)

* Then, I did an SSH into the NFS SERVER so that i could begin the configuration.
* To confirm the attached storage blocks, I ran the command:

  lsblk

![Screenshot from 2023-08-03 10-46-36](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/4be39bc9-4cad-4fd7-91cc-992a55dad204)

* Then i used the gdisk utility to partition each attached volume following the configuration prompts, selecting most appropriate options:

  sudo gdisk /dev/xvdb
  
  sudo gdisk /dev/xvdc
   
  sudo gdisk /dev/xvdd

* Then, I confirmed if the partitions were created as configured by running:

  lsblk
  
  ![Screenshot from 2023-08-03 11-06-53](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/12fd5aef-867e-4eda-b6d0-91f9af85d0bb)

* Then, I installed logical volume manager by running the command:

  sudo yum install lvm2

* Then, I used the pvcreate command to convert the partitions to LVM physical volumes by running comand:

  sudo pvcreate /dev/xvdb1

  sudo pvcreate /dev/xvdc1
  
  sudo pvcreate /dev/xvdd1

* Then, I verified that the physical volumes were created by running the following command:

  sudo pvs

  sudo lvmdiskscan

![Screenshot from 2023-08-03 11-23-38](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/3c9ee29c-31bb-4282-99ab-9a55792f1d51)


* Then I used the vgcreate utility to add all 3 PVs to a volume group (VG). I named the VG webdata-vg:

  sudo vgcreate webdata-vg /dev/xvdb1 /dev/xvdc1 /dev/xvdd1

* Then i confirmed that they were added to the VG by running command:

  sudo vgs

  
![Screenshot from 2023-08-03 11-33-41](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/7d37dc6b-77a2-4627-81ef-0012f528682d)

* Then, I used the lvcreate utility to create 3 logical volumes, namely:  lv-opt, lv-apps, and lv-logs.

  sudo lvcreate -n lv-opt -L 9G webdata-vg
  
  sudo lvcreate -n lv-logs -L 9G webdata-vg

  sudo lvcreate -n lv-apps -L 9G webdata-vg

* Then, I verified that the logical volumes had been created by running the command:

  sudo lvs

![Screenshot from 2023-08-03 11-43-17](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/f7f0aa07-4c3a-4f7a-82a9-44bb1fc79dce)

* Then, I verified the whole setup by running commands:

  sudo vgdisplay -v

  lsblk

![Screenshot from 2023-08-03 11-50-09](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/1768b156-6c5b-4fe1-acd2-0c8f0c44cc48)

* Then, I used mkfs.xfs to format the logical volumes with xfs filesystem, running the following commands:

  sudo mkfs -t xfs /dev/webdata-vg/lv-logs

  sudo mkfs -t xfs /dev/webdata-vg/lv-apps

  sudo mkfs -t xfs /dev/webdata-vg/lv-opt

* Then, I verified that the lms had been formated to xfs by running the command:

  blkid

![Screenshot from 2023-08-03 12-00-42](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/d3f24d77-85e0-4d9f-99d6-5d8c3f7f9fc8)


* Then, I created mount points on /mnt directory for the logical volumes as follow:
  
  Mount lv-apps on /mnt/apps – To be used by webservers

  Mount lv-logs on /mnt/logs – To be used by webserver logs

  Mount lv-opt on /mnt/opt – To be used by Jenkins server in future
    

* Then, I updated /etc/fstab file so that the mount configuration will persist after restart of the server.
The UUID of the logical volumes were used to update the /etc/fstab file;

![Screenshot from 2023-08-03 12-28-58](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/c231f033-b4d9-46a1-9870-550f54a225c6)

* Then I tested the configuration and reloaded the daemon

  sudo mount -a

  sudo systemctl daemon-reload

* After restarting daemon, to verify that the mounts are persistent, i ran the command:

  df -h

![Screenshot from 2023-08-03 12-36-38](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/302c00fa-b7c8-4f63-9afb-9d595aa58763)

* Then, I set up permission that allowed our Web servers to read, write and execute files on NFS by running the following commands:

  sudo chown -R nobody: /mnt/apps

  sudo chown -R nobody: /mnt/logs

  sudo chown -R nobody: /mnt/opt

  sudo chmod -R 777 /mnt/apps

  sudo chmod -R 777 /mnt/logs

  sudo chmod -R 777 /mnt/opt

  sudo systemctl restart nfs-server.service

* Then, I configured access to NFS for clients within the same subnet by editing /etc/exports file to input the following conent:

![Screenshot from 2023-08-04 10-59-51](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/d5fb0410-73dd-47e7-8a72-9462069c31e3)

* Then, I expoted the changes by runing the following command:

  sudo exportfs -arv

![Screenshot from 2023-08-04 11-01-52](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/c546c671-6938-4892-a761-4b5a5efbac12)

* Then, I checked which port was in used by NFS by runnning the command:
  
  rpcinfo -p | grep nfs

![Screenshot from 2023-08-04 11-10-48](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/9766363b-c432-4b63-a695-a535994192a9)

* Then, I opened the port (TCP 2049) by editing Security Groups (add new Inbound Rule) on the AWS console:     

Important note: In order for NFS server to be accessible from your client, you must also open following ports: TCP 111, UDP 111, UDP 2049

![Screenshot from 2023-08-04 11-25-44](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/2fe0a294-bfd4-412b-b78f-b425ffb840af)


### STEP 2- PREPARING THE WEBSERVERS:

* I launched a new EC2 instance (WEB-01) with RHEL 9 Operating System, then I installed NFS client:

  sudo yum install nfs-utils nfs4-acl-tools -y
  
* Then, I mounted /var/www/ and targeted the NFS server’s export for apps:
  
  sudo mkdir /var/www
  
  sudo mount -t nfs -o rw,nosuid NFS-Server-Private-IP-Address:/mnt/apps /var/www

* Then, I verified that NFS was mounted successfully by running:

  df -h

![Screenshot from 2023-08-04 12-51-54](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/ab3e5ea1-ff28-44cc-b4fe-e62ce6b21568)

  
* Then, I located the app folder for Apache on the Web Server and mount it to NFS server’s export for apps:

  sudo vi /etc/fstab
  
* Then, I added the following line:

  NFS-Server-Private-IP-Address:/mnt/apps /var/www nfs defaults 0 0

![Screenshot from 2023-08-04 12-56-39](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/70436b08-37b4-414d-85b8-a62009276389)

  
* Then I nstalled Remi’s repository, Apache and PHP
  
  sudo yum install httpd -y

  sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

  sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

  sudo dnf module reset php

  sudo dnf module enable php

  sudo dnf install php php-opcache php-gd php-curl php-mysqlnd

  sudo systemctl start php-fpm

  sudo systemctl enable php-fpm

  setsebool -P httpd_execmem  (very important to make changes persistent)
  
* Then, I repeated the steps above for another 2 Web Servers (WEB-02 & WEB-03).

* Then, I verified that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps. 

![Screenshot from 2023-08-04 14-22-47](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/835bdfdf-a09b-48e1-a090-b29a14894ec6)

* Then, I located the log folder for Apache on the Web Server and mount it to NFS server’s export for logs:

  sudo vi /etc/fstab
  
* Then, I added the following line:

  NFS-Server-Private-IP-Address:/mnt/logs /var/log/httpd nfs defaults 0 0

![Screenshot from 2023-08-04 14-50-47](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/0b0596ed-1017-41c2-80cf-c8f60fda116d)



### STEP 3- PREPARING THE DATABASE SERVER:

* I updated apt repositories by running the command:

  sudo apt update -y

* Then, I installed mysql server by running the command:

  sudo apt install mysql-server

* Then, I verified that msql was succesfully installed:

![Screenshot from 2023-08-04 12-02-01](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/71259953-1b40-4144-9d60-f91b8ac780e5)

* Then, I entered into mysql mode with the command:

  sudo mysql

* Then, i created a database and named it tooling with command:

  CREATE DATABASE tooling;

* Then, I created a user and named it WEBACCESS with comand:

  CREATE USER 'WEBACCESS'@'ip of web server(s)' IDENTIFIED BY 'password';

* Then, I granted all permissions on tooling to WEBACCESS with command:

  GRANT ALL privileges ON tooling.* TO 'WEBACCESS'@'172.31.80.0/20';

  FLUSH PRIVILEGES;

* To verify the setup, I ran the command:

  show databases;

![Screenshot from 2023-08-05 11-35-17](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/70b8a38f-7afe-4cfe-8573-68de7b9d12c6)

* Then i edited the security group of my DB server to allow traffic from the cidr block of my webservers from port 3306:

![Screenshot from 2023-08-05 12-21-39](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/e1d30cef-b41b-4fff-805b-7c6785818afe)

* Then I edited the file /etc/mysql/mysql.conf.d/mysqld.cnf changing binding address to 0.0.0.0

  
![Screenshot from 2023-09-07 19-53-00](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/dcee1167-7ac4-4f91-9eab-da1150805180)


### STEP 4- INSTALLING MYSQL CLIENT ON WEBSERVERS AND CONNECTING WITH DB SERVER:   
* I forked the tooling repo to my account
* Then, I installed git on my webservers:

    sudo yum -y install git.

* Then, I cloned the repository on my webservers:

    git clone https://github.com/AbooHamzah/tooling.git

* Running an ls command the tooling folder appears.

![Screenshot from 2023-09-07 20-31-46](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/275e9264-cf53-4d3e-a6d6-2361deb94b9a)

* Then I did a cd into the tooling/html/ and copied its contents into /var/www/html

  cd tooling/html/
  
  sudo cp -R . /var/www/html/

  
* I installed MySQL client on all 3 WEB SERVERS and tested that I could connect from the Web Servers to the DB server by using mysql-client, I thus ran the commands:
  
  sudo yum install mysql
  
  sudo mysql -u admin -p -h DB-Server-Private-IP-address

![Screenshot from 2023-08-05 12-31-18](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/f86bbcb7-a6c0-4a0c-92d6-0e019aa0dffc)


* Then, I edited the functions.php file to add credentials of the DataBase:

    // connect to database
  
    $db = mysqli_connect('ip_addr', 'user', 'password', 'tooling');

* Then I restarted the html:

    sudo systemctl restart httpd

* Then, I did a cd into the tooling folder and applied the tooling-db.sql script to the database using this command:

 
   mysql -h (databse-private-ip) -u (db-username) -p database < tooling-db.sql

* Then, I restarted msql server and apache server.

  sudo systemctl restart mysql.service (on the DB server)
  
  sudo systemctl restart httpd (on the WEB servers)

* Then, I tested all three WEB public ips' on a browser.

![Screenshot from 2023-08-14 14-21-29](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/4844b371-6b47-4386-8310-fd6b90ca44b6)

![Screenshot from 2023-08-21 12-28-22](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/8d95345b-ef4b-45ef-b7e4-aff4b87dcbd7)


* Then, I went back to the DB server and added another user using the command

  INSERT INTO users (username, password, email, user_type, status) VALUES ('myuser', '5f4dcc3b5aa765d61d8327deb882cf99', 'user@mail.com', 'admin', 1);

* Then, I confirmed that the entry had been inserted into the table "users" by running the command:

  SELECT * FROM users;

![Screenshot from 2023-08-21 12-55-21](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/b3917456-fe70-458e-944f-5e2211ac06b7)

* Then, I logged into the tooling website with the new user 'myuser' and password "password":

![Screenshot from 2023-08-21 12-52-31](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/6f804bd8-d995-4c57-ae2b-244ada5b1dd4)

![Screenshot from 2023-08-21 12-52-45](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/f5b3acb3-630b-4384-8776-c447c0746598)








