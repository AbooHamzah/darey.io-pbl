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
