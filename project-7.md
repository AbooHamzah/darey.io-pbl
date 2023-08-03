![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/a53d9fc2-f2dc-47dc-b9cd-29032cbe3499)## DEVOPS TOOLING WEBSITE SOLUTION
* I implemented a tooling website solution which makes access to DevOps tools within the corporate infrastructure easily accessible. This solution that consists of following components:

1) Infrastructure: AWS
2) Webserver Linux: Red Hat Enterprise Linux 8
3) Database Server: Ubuntu 20.04 + MySQL
4) Storage Server: Red Hat Enterprise Linux 8 + NFS Server
5) Programming Language: PHP
6) Code Repository: GitHub

* I utilized the 3-tier Web Application Architecture with a single Database and NFS server as a shared file storage:
  
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


