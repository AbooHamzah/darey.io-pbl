## TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS.
In this project, I enhanced the architecture prepared in Project 8 by adding a Jenkins server, I then configured a job to automatically deploy source codes changes from GitHub to the NFS server.
Here is how the updated architecture would be upon competition of this project:

![add_jenkins](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/7c951328-d259-4c90-bccc-0807b6082d8a)


### Step 1 – Installation of Jenkins server
* I spun up an AWS t2.micro EC2 instance, named it JENKINS01 and loaded an Ubuntu 22.04 AMI into it.
* Then, I Installed JDK (since Jenkins is a Java-based application) using the  following command:
  
  sudo apt update
  
  sudo apt install default-jdk-headless

  * Then, I installed Jenkins with the following command:

   #Install Jenkins Server on Ubuntu Server
  curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
  
  echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
  
  sudo apt-get update

  sudo apt-get install -y jenkins

  * Then, I checked the status of jenkins to ensure it was running by running the command:
 
    sudo systemctl status jenkins

  ![Screenshot from 2023-08-25 10-16-19](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/13230a68-db8b-49fc-8c9c-d747dbab5e19)

  * Then, on the JENKINS server, i opened the port 8080 as the default Jenkins server uses TCP port 8080.
  * Then, from a browser, I accessed the JENKINS SERVER by public IP and port 8080.
 
  ![Screenshot from 2023-08-25 10-21-27](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/074fa6a2-d433-4c5d-9b3c-541e72db7d35)

  * Then, I followed the path instructed by the getting started page to get the initial admin password by running the command:

    sudo cat /var/lib/jenkins/initialAdminPassword

  * Then, I pasted it to Unlock jenkins and begin configuration process:
 
    ![Screenshot from 2023-08-25 10-24-50](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/52f0009b-4ace-4970-b689-fc3b65ea7a6b)

* Then, I selected "Install suggested plugins"

![Screenshot from 2023-08-25 13-35-23](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/0821f1db-cd88-428d-8993-6b6b0b672977)

![Screenshot from 2023-08-25 13-35-37](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/69b63b9b-ff7b-4469-ab97-14e1225a8004)


### Step 2 – Configuring Jenkins to retrieve source codes from GitHub using Webhooks

* I enabled webhooks on the tooling repository putting in the url and port number of the Jenkins server, I also changed content type to "application/json":

![Screenshot from 2023-08-25 13-43-52](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/cc1e7cef-729c-4625-9d1b-bc5abbbcc1b6)

* Then, I created a jenkins freestyle project and named it tooling_github

![Screenshot from 2023-08-25 13-53-16](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/869795f7-df31-45eb-a3c1-3078631f4517)

* Then, in configuring of the Jenkins freestyle project I chose Git repository, using the url of the tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

![Screenshot from 2023-08-25 14-59-03](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/a9c70f0c-9f9d-4319-995e-ceafde98becf)

* Then, I saved the configuration and clicked on the "Build Now" button to build the project, it was successful and i was able to view that in #1
  
![Screenshot from 2023-08-25 14-59-45](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/66f640d9-58e4-49fc-bd71-b385a2b23af0)

* Then, I opened the build and checked in "Console Output" that it ran succesfully

  ![Screenshot from 2023-08-25 15-02-39](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/ef0f9aba-5376-4feb-b7e1-5fa6c1ccbfd7)

### N/B the above process is manual and only runs when you trigger the build manually. In the following Step, we would configure jenkins to automate a build(i.e when there is a change to the code base).

### Step 3 - Configuring Jenkins to automate builds

* I clicked on the configure option on the job and added the following configurations:
  
  1) under "build triggers", i selected "GitHub hook trigger for GITScm polling"
  2) under "Post-build Actions" i selected "archive the artifacts" and then i chose that all files should be archived.
 
![Screenshot from 2023-08-25 15-13-27](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/bd76f417-84d3-424c-bad0-66078d54c646)

![Screenshot from 2023-08-25 15-13-43](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/b4cb588f-fc82-4bd4-8004-88652e0f848a)

* Then, I edited the README.md file file under tooling and and pushed the changes to master branch. going back to jenkins, it was clear that another build had occured, this time automatically trigered by the change in our tooling project.

![Screenshot from 2023-08-25 15-26-42](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/c2d0c6b6-e472-4bc6-8ff2-0e1b498fa6a5)

*  Due to the configuration above, that artifacts should be archived, the artifacts were stored locally on the Jenkins server:

![Screenshot from 2023-08-25 15-37-53](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/27c8606f-5679-4098-93ea-074bbfd34871)

### Step 4 – Configuring Jenkins to copy files to NFS server via SSH

* I installed the "Publish Over SSH" plugin.

![Screenshot from 2023-08-25 15-47-00](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/f673439a-60ff-42bf-91d2-d7a8a5bbf328)

* Then, I configured Jenkins to copy artifacts over to NFS server. Providing private key of NFS server, "ip address" as hostname, ec2-user as username and /mnt/apps as Remote Directory.

  ![Screenshot from 2023-08-25 16-07-31](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/1d7307c2-0190-4f9f-b240-32e1c0d987b0)

* Then, I tested the connection and saved the configuration.
* Then I configured the job under Post build Action to Send build artifacts over SSH, selecting all artifacts to be transfered.

![Screenshot from 2023-08-25 16-11-36](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/61ee21eb-ae7d-433e-a18d-b1db7ec628e7)

![Screenshot from 2023-08-25 16-13-02](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/595b35c1-8235-4e05-a581-ce1263d99a24)

* Then, I edited the README.md file in my tooling repo and pushed the change, automatically another build occured on Jenkins as expected, but it was not succesful. giving the below error.

![Screenshot from 2023-08-25 16-38-25](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/e79a1bdb-930c-4d6a-8ec2-71eb603e2d67)

##### * Upon trouble-shooting I found that the /mnt/apps folder had ownership issues, I then used the following command to open up ownership:

  sudo chmod -R 777 /mnt/apps
  
  sudo chown -R nobody:nobody /mnt/apps

* Then, I re-edited the README.md file and pushed:

![Screenshot from 2023-08-25 21-02-05](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/30d47dbb-ce54-47b8-9439-93925108e5dd)

* To confirm if the artifacts were sent to the NFS server, i ran a tail command on the NFS server

  ![Screenshot from 2023-08-25 21-05-51](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/a1cd2351-4653-416c-b3ad-906398a5c8a9)

* Thus, confirming that the files were sent over SSH.



  















  

