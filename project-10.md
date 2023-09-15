## LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS

### Task:
This project consisted of two parts:

1) Configure Nginx as a Load Balancer.
2) Register a new domain name and configure secured connection using SSL/TLS certificates.

The target architecture will look like this:

![Screenshot from 2023-09-15 10-43-12](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/dce07b21-f645-4270-8b7b-e101f72cf5e9)


### STEP 1- CONFIGURING NGINX AS A LOAD BALANCER:

- I created an EC2 instance based on Ubuntu Server 22.04 LTS and named it project10-NGINX-LB  opening TCP port 80 for HTTP connections, also TCP port 443 for secured HTTPS connections.

![Screenshot from 2023-09-15 10-53-20](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/73186e57-7310-4813-92ab-59d52883eabe)

 - Then, I updated the /etc/hosts file for local DNS with Web Servers’ names (i.e. web1 and web2) and their local IP addresses

![Screenshot from 2023-09-15 10-58-54](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/57eab383-24df-4580-8b84-247b31fa40a5)

- Then, I installed and configureed Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers using commands:

  sudo apt -y update

  sudo apt install nginx
  
![Screenshot from 2023-09-15 11-04-50](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/ae61ca6c-6a42-4990-b540-671acac33f53)

- Then, I configured Nginx LB using Web Servers’ names defined in /etc/hosts file by editing the default nginx configuration file(sudo vi /etc/nginx/nginx.conf) with the following:

  upstream myproject {
  
    server web1 weight=5;
  
    server web2 weight=5;
  
  }

server {

    listen 80;
    
    server_name www.toolingexample.online;
    
    location / {
    
      proxy_pass http://myproject;
      
    }
    
  }

#### N/B: I commented out the below line:

       include /etc/nginx/sites-enabled/*;



- Then, I restarted Nginx and made sure the service was up and running using commands:

  sudo systemctl restart nginx
  
  sudo systemctl status nginx

![Screenshot from 2023-09-15 11-00-45](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/1920aeb2-6a24-41b8-bf7d-9c6863977c73)


- Then I tested the public ip of the NGINX-LB in a browser:

![Screenshot from 2023-09-15 11-43-32](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/a134a070-b200-47f4-a21b-9f44ee3af784)


### STEP 2 - REGISTERING A NEW DOMAIN NAME, ATTCAHING OUR NGINX-LB PUBLIC IP ADDRESS AND CONFIGURING SECURED CONNECTION USING SSL/TLS CERTIFICATES

- I registered the domain name toolingexample.online with GoDaddy

![Screenshot from 2023-09-15 11-55-38](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/5cf9c984-60dd-4381-b6ce-e31642b9efbb)

- Then, I updated the A record in GoDaddy to point to Nginx LB using Elastic IP address

![Screenshot from 2023-09-15 11-58-02](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/45745e47-0e4f-49f1-b5fd-593be0461dc1)

- Then, I configured Nginx to recognize the domain name (www.toolingexample.online), updating the nginx.conf with server_name www.toolingexample.online instead of server_name www.domain.com

![Screenshot from 2023-09-15 12-01-05](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/bb887be5-59e1-4246-a0ec-fba9bc88999e)

- Then, I installed certbot and requested for an SSL/TLS certificate

### Make sure snapd service is active and running by running (sudo systemctl status snapd)

- Then, I installed certbot

  sudo snap install --classic certbot

- Then, I requested for TLS certificate following the certbot instructions 

  sudo ln -s /snap/bin/certbot /usr/bin/certbot
  
  sudo certbot --nginx

![Screenshot from 2023-09-15 12-09-22](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/52ce9fc4-97b3-4015-904b-b0fa162f9546)

- Then I tried reaching out domain by using https:

![Screenshot from 2023-09-15 12-12-26](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/acbd2b5c-a915-4ff4-a03a-1ca4aa065e40)

