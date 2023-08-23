## LOAD BALANCER SOLUTION WITH APACHE

In this project, I enhanced the Tooling Website solution(Project 7) by adding a Load Balancer to disctribute traffic between the multiple Web Servers and allow users to access our website using a single URL.

![Tooling-Website-Infrastructure-wLB](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/22ad0dd4-d041-41ea-a242-35e927b1097d)

## PREREQUISITE CONFIGURATION:
* Apache(httpd) process is up and running on both WEB SERVERS

* ‘/var/www/html’ directories of both Web servers are mounted to ‘mnt/apps’ of the nfs servers

* Client browser can access both Web Servers by their respective Public IP addresses or Public DNS names and can open the Tooling Website.

![prerequisites-project8](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/e1ef7682-cb95-4ab8-b1e3-42fd2490357b)

  
### STEP 1- CONFIGURE APACHE AS A LOAD BALANCER
* I started 2 0f 3 webservers configured in project 7.
* Then, I started up NFS server and DB server.
* Then, I provisioned an EC2 instance naming it Project8-Apache-LB, with Ubuntu 22.04 AMI and Instance type as t2.micro.
* Then, I opened up ports 80 and 443 to allow http and https traffic respectively.
* Then, I started the Instance.
  
![Screenshot from 2023-08-23 10-01-33](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/c6778ad0-9744-44be-a791-e3b7c08f5015)

* Then, I wrote a bash script named apache-lb.sh containing the following commands:

  #This command updates the package list
  
  sudo apt update
  
  #This command installs Apache

  sudo apt install apache2 -y

  #This command installs the libxml2-dev package, which is a development library for the libxml2 library, which is used by Apache.
  
  sudo apt-get install libxml2-dev

  #These commands below enable the Apache modules that are required for load balancing. The rewrite module is used to rewrite URLs, the proxy module is used to proxy requests to other servers, the proxy_balancer module is used to balance traffic between multiple servers, the proxy_http module is used to proxy HTTP requests, the headers module is used to add headers to responses, and the lbmethod_bytraffic module is used to balance traffic based on traffic.
  
  sudo a2enmod rewrite
  
  sudo a2enmod proxy
  
  sudo a2enmod proxy_balancer
  
  sudo a2enmod proxy_http
  
  sudo a2enmod headers
  
  sudo a2enmod lbmethod_bytraffic

  #Restart apache2 service
  
  sudo systemctl restart apache2
  
* Then, I executed the bash script by running the command:

  bash apache-lb.sh
  
* Then I confirmed that apache2 is up and running using the command:

  sudo systemctl status apache2
  
![Screenshot from 2023-08-23 10-32-54](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/8a8ffe4b-4618-4ef2-93e8-8253cb45ac4e)

* Then I edited the /etc/apache2/sites-available/000-default.conf file, adding the following:
  
  ![Screenshot from 2023-08-23 15-53-57](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/9a66f370-615e-4cfe-bcba-7234657b6e8d)

* Then I restarted the apache server:

  sudo systemctl restart apache2

* Then, I Verified that the configuration worked – by trying to access your LB’s public IP address or Public DNS name from my browser:
  
![Screenshot from 2023-08-23 10-51-37](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/0acaa860-311f-427c-9613-16deee2156fe)

*Then, I verified that the request to each server was equal by running the following command on both servers and then refreshing the browser containing LB ip address multiple times.

  sudo tail -f /var/log/httpd/access_log

![Screenshot from 2023-08-23 11-21-14](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/5594bd34-1690-4b7a-a157-0b15604f1465)

### STEP 2- CONFIGURING LOCAL DNS NAME RESOLUTION

* I edited the /etc/hosts file to attach names to the WEB private ip addresses:

![Screenshot from 2023-08-23 11-27-25](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/39debbda-8914-4e47-82a1-e0cdc011aec6)

* Then, I edited the LB config file replacing the ip-addresses with the given names:

![Screenshot from 2023-08-23 11-29-54](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/2fb7d3e0-5fda-4cf3-bf3f-ee35a711f1fe)

* Then, on the terminal of the LB server I did a curl of the Locally Resolved Names attached to WEB1 and WEB2:

![Screenshot from 2023-08-23 11-40-24](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/a9086ba2-bc3a-42b9-a313-8f34741fbbae)

![Screenshot from 2023-08-23 11-40-47](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/b74246ec-1ae1-40cc-934a-1a5c47695baf)
