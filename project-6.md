## WEB SOLUTION WITH WORDPRESS

### STEP 0 - LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER”:
* I provisioned an EC2 instance(WEB SERVER) selecting a RedHat AMI and t2.micro as instance type, then I attached three 10Gb EBS volumes to the instance:
  
![Screenshot from 2023-07-26 17-37-59](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/9e490ac5-b562-41e5-8fea-d8ccc8602b1d)

  
### STEP 1 - CONFIGURATING EBS OF WEBSERVER TO BECOME MOUNTABLE LOGICAL FILESYSTEMS:

* I connected to WEB SERVER and confirmed if the volumes were attached by running the command:

  lsblk

![Screenshot from 2023-07-26 17-58-20](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/35625a6d-9673-46e6-94cc-f4d035846f73)

* Then, I checked to see all mounts and free space on your server by running the command:

  df -h

* Then, I used gdisk utility to create a single partition on each of the 3 disks:

  sudo gdisk /dev/xvdf
  
  sudo gdisk /dev/xvdg
  
  sudo gdisk /dev/xvdh

![Screenshot from 2023-07-28 11-39-22](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/1bef49a0-9b27-4691-a0b5-5862d3b9465e)

* Then I installed the lvm2 package using the command:

   sudo yum install lvm2

* Then, I checked to confirm for available partitions using the command:

  sudo lvmdiskscan

![Screenshot from 2023-07-28 11-45-07](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/c72424ac-8ca6-48f9-ad91-f4534fe5abe7)

* Then, I used the pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM:

  sudo pvcreate /dev/xvdf1
  
  sudo pvcreate /dev/xvdg1
  
  sudo pvcreate /dev/xvdh1


![Screenshot from 2023-07-28 11-49-55](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/277768cd-6183-4c25-9455-857c4019edeb)

* Then, I confirmed that the pertitions have been converted to physical volumes by running the command:

  sudo pvs
  
![Screenshot from 2023-07-28 11-50-44](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/8ff5afc1-fa82-43f4-af2c-b993e9644741)

* Then, I used vgcreate utility to add all 3 physical volumes (PVs) to a volume group (VG), naming the VG webdata-vg:

  sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
  
* Then I verified that the volume group was created by running the command:

  sudo vgs
  
![Screenshot from 2023-07-28 11-55-48](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/08308e9f-2f1e-49cc-bcbd-c6972501425e)

*Then I used lvcreate utility to create 2 logical volumes. apps-lv (half of the PV size), and logs-lv  (the remaining space of the PV size). NOTE: apps-lv was used to store data for the Website while, logs-lv was used to store data for logs.

  sudo lvcreate -n apps-lv -L 14G webdata-vg

  sudo lvcreate -n logs-lv -L 14G webdata-vg

* Then I verified that the two logical volumes were created by runningthe command:

  sudo lvs

![Screenshot from 2023-07-28 12-01-58](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/5d9917a0-6bac-43c2-8961-d655810b5457)

*Then, I verified the entire setup by running the commands:

  sudo vgdisplay -v #view complete setup - VG, PV, and LV
  
  sudo lsblk

![Screenshot from 2023-07-28 12-17-07](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/dc962826-92cd-485a-8a71-164c015e42ea)

* Then, I used mkfs.ext4 to format the logical volumes with ext4 filesystem:
 
  sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
  
  sudo mkfs -t ext4 /dev/webdata-vg/logs-lv

![Screenshot from 2023-07-28 12-21-51](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/196f4b9e-fc48-4d90-8f46-726135c552b5)

* Then I created /var/www/html directory to store website files using the command:

  sudo mkdir -p /var/www/html

* Then, I created /home/recovery/logs to store backup of log data using the command:

  sudo mkdir -p /home/recovery/logs

* Then I mounted /var/www/html on the apps-lv volume using the command:

  sudo mount /dev/webdata-vg/apps-lv /var/www/html/

* Then, I used the rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system):

  sudo rsync -av /var/log/. /home/recovery/logs/

* Then, I mounted /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted.       That is why the step above is very important):
  
    sudo mount /dev/webdata-vg/logs-lv /var/log
  
*  Then I restored log files back into /var/log directory
 
    sudo rsync -av /home/recovery/logs/. /var/log/

*   Then I ran the following command to get the UUIDs' I need to edit the /etc/fstab file:

    sudo blkid
    
![Screenshot from 2023-07-28 13-09-19](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/eb5b4579-a7c4-490b-a335-f11933192cea)

*  Then I edited the /etc/fstab file to include the UUIDs' of the logical volumes so that the mount configuration will persist after restart of the server:

![Screenshot from 2023-07-28 14-42-02](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/eab450b6-5a68-4650-be17-6006d66d2182)

* Then, I tested the configuration and reloaded the daemon using the commands:

    sudo mount -a
    sudo systemctl daemon-reload
  
* Then, I verified that the logical volumes were mounted appropriately by running the command:

    df -h

![Screenshot from 2023-07-28 14-52-08](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/0609271f-0c2b-46f6-b2d4-b4c2a8d53dbc)

### STEP 2 - CONFIGURATING EBS OF DATABASE TO BECOME A SINGLE MOUNTABLE LOGICAL FILESYSTEM:

* I followed the same steps as for the WEB SERVER, but instead of apps-lv I created db-lv and mounted it to /db directory instead of /var/www/html/.

  ![Screenshot from 2023-07-28 15-59-25](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/1e06cab8-705b-494b-ae66-645f5c91d4de)

### STEP 3 - INSTALLING WORDPRESS ON WEBSERVER

* I updated RHEL packages using the command:
  
    sudo yum -y update
   
* Then, I installed wget, Apache and it's dependencies:

    sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json

* Then, I started Apache:

  sudo systemctl enable httpd
  sudo systemctl start httpd
  sudo systemctl status httpd

![Screenshot from 2023-07-28 16-27-09](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/f47e836f-1278-4c8d-8223-e441dc7c03c4)

* Then, I installed php and it's dependencies using the following commands:

    sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
  sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
  
  sudo yum module list php
  
  sudo yum module reset php
  
  sudo yum module enable php:remi-7.4
  
  sudo yum install php php-opcache php-gd php-curl php-mysqlnd
  
  sudo systemctl start php-fpm
  
  sudo systemctl enable php-fpm
  
  setsebool -P httpd_execmem 1

* Then, I restarted Apache using commands:

    sudo systemctl restart httpd
* Then, I downloaded wordpress and copied it to the var/www/html folder using the following commands:
     mkdir wordpress
  
  cd   wordpress
  
  sudo wget http://wordpress.org/latest.tar.gz
  
  sudo tar xzvf latest.tar.gz
  
  sudo rm -rf latest.tar.gz
  
  sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
  
  sudo cp -R wordpress /var/www/html/

 ![Screenshot from 2023-07-28 19-44-30](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/59740551-83fb-4e55-87dc-ca5ee2cde37e)

* Then, I configured SELinux policies by runnibg the following commands:

  sudo chown -R apache:apache /var/www/html/wordpress
  
  sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
  
  sudo setsebool -P httpd_can_network_connect=1

### STEP 4 - INSTALLATION OF MYSQL ON DB SERVER

*  I updated RHEL packages using the command:
  
    sudo yum -y update

* Then, I installed, started and enabled mysql server using the following commands:

    sudo yum install mysql-server
  
    sudo systemctl restart mysqld
  
    sudo systemctl enable mysqld

    sudo systemctl status mysqld

![Screenshot from 2023-07-28 20-57-45](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/9ea101d5-bd31-45c7-80ee-91196cefeb3e)

### STEP 5 - CONFIGURING DB TO WORK WITH WORDPRESS

* I configured my DB to work with wordpress by running the following commands:

    sudo mysql
  
    CREATE DATABASE wordpress;
  
    CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
  
    GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
  
    FLUSH PRIVILEGES;
  
    SHOW DATABASES;
  
    exit

![Screenshot from 2023-07-28 21-19-26](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/9e8b900e-09a6-4ee0-8518-4e6e82d07af0)


### STEP 6 - CONFIGURING WORDPRESS TO CONNECT TO REMOTE DATABASE

* I configured the security group of my DB server to allow MySQL traffic by opening the port 3306 and specifying ip address of the WEB SERVER:

![Screenshot from 2023-07-28 21-27-43](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/4a1f5905-4766-42b6-b34a-7463f504afa1)

* Back on my WEB SERVER, I Installed MySQL client and tested that you can connect from the Web Server to the DB server by using mysql-client:

  sudo yum -y install mysql

  sudo mysql -u user -p -h <DB-Server-Private-IP-address>

* Then, still on the WEB SERVER, I verified that i could view the databases created in the DB server:

    SHOW DATABASES;
  
![Screenshot from 2023-07-28 21-39-27](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/793ba0e5-1372-4957-9b91-689f80d0ccf1)


* Then, I configured the security group of my WEBSERVER instance to allow HTTP traffic on port 80 from anywhere.

![Screenshot from 2023-07-28 21-50-51](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/d7541312-9811-49ac-b15a-998982efdfd0)

* Then, I edited the config file inside the wordpress folder to add database name, user name, password and hostname as appropriate.

* Then, I restarted Apache to effect the change using the command:

    sudo systemctl restart httpd
  
* Then, I Tried to access the WEBSERVER browser the link to your WordPress:

 
   http://<Web-Server-Public-IP-Address>/wordpress/

![Screenshot from 2023-07-28 22-10-42](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/6d651fc1-ff77-4fd8-86c9-26dc21b280da)

![Screenshot from 2023-07-28 22-10-33](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/3548c962-bb5c-4baa-aab7-4b156b412cfb)

![Screenshot from 2023-07-28 22-10-04](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/e4835070-0f94-4959-9ef4-c9351b64d158)

