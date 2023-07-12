## MEAN STACK DEPLOYMENT TO UBUNTU IN AWS
In this project, I implemented a simple Book Register web form on AWS using MEAN stack.

### MEAN Stack is a combination of following components:
- MongoDB (Document database) – Stores and allows to retrieve data.
- Express (Back-end application framework) – Makes requests to Database for Reads and Writes.
- Angular (Front-end application framework) – Handles Client and Server Requests.
- Node.js (JavaScript runtime environment) – Accepts requests and displays results to end user.

### Step 0 – Preparing prerequisites
* I set up an EC2 instance using Ubuntu 20.04 AMI and t2.micro as instance type and connected to the Ubuntu server via ssh.
![Screenshot from 2023-07-11 20-39-36](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/9a0dbee6-a3b8-44bf-9f1d-f9ed751d77c5)

### STEP 1 – BACKEND CONFIGURATION:
* I updated a list of packages in package manager uisng: sudo apt update.
* Then, I upgraded all installed packages using: sudo apt upgrade.
* Then, i installed certificates using the following:
 sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
 curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
* Then, I installed NodeJs and npm using the command:sudo apt-get install nodejs and sudo apt install npm

### Step 2 - Install MongoDB:
* I ran the following commands to install needed dependencies for MongoDB:
  sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
  echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
* Then, I installed mongodb using the following command: sudo apt install -y mongodb
* Then, I started the mongodb server using the command: sudo service mongodb start
* Then, I confirmed the server was running by using the command: sudo systemctl status mongodb
   
![Screenshot from 2023-07-11 21-28-15](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/00a435d2-31df-450c-b40c-4a06a331eda4)

* Then, I installed the ‘body-parser’ package to help us process JSON files passed in requests to the server: sudo npm install body-parser.
* Then, I created a directory 'Books' and did a cd into it: mkdir Books && cd Books
* Then In the Books directory, I initialized npm project: npm init
* Then, I created and edited a server.js file inputing the following:
![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/2b6d8b9e-7132-4890-b3ed-e78c1a4ec91d)
  
### Step 3 - Install Express and set up routes to the server:
* I installed the Mongoose package which provides a straight-forward, schema-based solution to model the application data: sudo npm install express mongoose
* Then I created an apps directory did a cd into it and created a routes.js file inputing the following:
  
![Screenshot from 2023-07-11 21-54-03](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/ef00cff0-51f2-4e74-a121-290416f841f2)

*Then, I created a models directory and did a cd into it:mkdir models && cd models
*Then, I created a books.js file and inputed the following:

![Screenshot from 2023-07-11 21-56-53](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/2712b397-160b-478b-a790-5d0186cbc94d)

### Step 4 – Access the routes with AngularJS:
* Under the Books directory, i created a public directory and did a cd into it: mkdir public && cd public
* Then, I created a script.js file and inputed the following:

![Screenshot from 2023-07-11 22-02-29](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/fe09211f-f1b5-4292-ae2e-cf812388edf2)

* Then, I created a index.html file and inputed the following:

![Screenshot from 2023-07-11 22-06-49](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/012734f3-5b56-450d-ae4f-2ce4acd33963)

* Then, I edited my EC2 security group to open port 3300 to the internet, so that i can access the App from a browser.
* Then, in confirmation that the Node App was successfully deployed on port 3300 of the EC2 instance, I accessed the public Ip and port number of the instance through a browser:
![Screenshot from 2023-07-12 12-21-02](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/914622f5-aa5f-40ac-98e4-2150ae38d68c)



