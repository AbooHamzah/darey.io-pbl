## WEB SOLUTION WITH WORDPRESS

### STEP 0 - LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER”:
* I provisioned an EC2 instance(WEB SERVER) selecting a RedHat AMI and t2.micro as instance type, then I attached three 10Gb EBS volumes to the instance:
  
![Screenshot from 2023-07-26 17-37-59](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/9e490ac5-b562-41e5-8fea-d8ccc8602b1d)


* Then, I connected to WEB SERVER and confirmed if the volumes were attached by running the command:

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
