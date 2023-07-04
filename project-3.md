## SIMPLE TO-DO APPLICATION ON MERN WEB STACK
In this project, I implemented a TO-DO application on MERN stack in AWS Cloud.

### MERN Web stack consists of following components:

MongoDB: A document-based, No-SQL database used to store application data in a form of documents.
ExpressJS: A server side Web Application framework for Node.js.
ReactJS: A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.
Node.js: A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

### Step 0 – PREPARING PREREQUISITES:
* I set up an EC2 instance using Ubuntu 20.04 AMI and t2.micro as instance type and connected to the Ubuntu server via ssh.
  ![Screenshot from 2023-07-03 10-29-49](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/fc588595-56b6-43da-b683-13fcda51e481)

### STEP 1 – BACKEND CONFIGURATION:
* I updated a list of packages in package manager uisng: sudo apt update.
* Then, I upgraded all installed packages using: sudo apt upgrade.
* Then, I installed the Node.js 18.x LTS version using: curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - .
* Then, I installed nodejs and npm using the command: sudo apt-get install -y nodejs
* Then, I verified that node has been installed by running: node -v
* Then, I verified that npm has been installed by running: npm -v
* Then, I created a new directory for the new project: mkdir Todo
* Then, I changed my directory to the just created Todo directory using: cd Todo
* Then, I initialised the project, so that a new file named package.json was created. This file will normally contain information about your application and the dependencies that it needs to run: npm init .![Screenshot from 2023-07-03 11-15-31](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/fed73bbc-da50-46fa-95d1-2a1e121c60af)
  ![Screenshot from 2023-07-03 11-15-31](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/f17ae083-84bd-4601-9c4c-103ea5b77e2b)
* Then, I installed expressjs by using: npm install express
  ![Screenshot from 2023-07-03 11-23-06](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/ff2b0135-bbff-4f62-8541-43bf01740836)
* Then, I created index.js file using: touch index.js
* Then, I installed a dotenv module using: npm install dotenv
* Then, I edited the index.js file using vim editor
  ![Screenshot from 2023-07-03 11-51-45](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/bb5efcaf-4acb-46ac-9466-08f1362e29ac)
* Then, while in the directory containing my index.js file, i started up the node server by running: node index.js
  ![Screenshot from 2023-07-03 11-54-52](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/2c65603d-09c7-44ac-8e30-283abf687a39)
* Then, i edited the EC2 instance security group to allow inbound traffic via port 5000:
  ![Screenshot from 2023-07-03 12-17-06](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/b8eddb24-6748-4561-8f1c-96d199fedbcd)
* Then, I tested if i could access the server over the internet by using the public ip and opened port:
  ![Screenshot from 2023-07-03 12-33-19](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/eaca8374-3089-4bbc-a247-f86d23d303c9)
* Then, I created a routes directory using: mkdir routes
* Then, I created an api.js file using: touch api.js
* Then, I edited the api.js file:
  ![Screenshot from 2023-07-03 12-47-24](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/d00e121c-a839-4a89-924c-c7c4ff47dd03)
* Then, I installed mongoose which is a Node.js package that makes working with mongodb easier: npm install mongoose
  ![Screenshot from 2023-07-03 13-06-37](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/de84d38d-6cf6-4592-a2f6-60ee76affd5e)
* Then, I created a directory models in the Todo directory using: mkdir models
* Then, I created a file todo.js in the models directory: touch todo.js
* Then, I edited the todo.js file using vim:
  ![Screenshot from 2023-07-03 13-13-59](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/22ea9fb3-b1e0-4015-90f5-07b3a1f375c1)
* Then, I set up mongobd database via mLab, which provides MongoDB database as a service solution (DBaaS).
* Then, i created a .env file and edited it by inputing a connection string in it to ensure connection with the database.
    ![Screenshot from 2023-07-03 16-01-51](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/a810ec5b-06b0-4217-8a32-b25b89c579f0)
* Then, I

