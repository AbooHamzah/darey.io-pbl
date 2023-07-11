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
* Then, I initialised the project, so that a new file named package.json was created. This file will normally contain information about your application and the dependencies that it needs to run: npm init .
  
![Screenshot from 2023-07-03 11-15-31](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/fed73bbc-da50-46fa-95d1-2a1e121c60af)

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

* Then, I ran the node server usng the command: node index.js, to ensure that our backend has been sucessfully configured.
  
![Screenshot from 2023-07-10 07-21-14](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/4a7e999a-95ee-4341-a010-97f012cace8d)

*Then, I set up postman to test the API:

![Screenshot from 2023-07-10 14-59-15](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/a992fe02-f2ac-4119-8a56-b116ef81e7ca)

#### STEP 2 – FRONTEND CREATION
* I created a client directory to keep all react code using:  npx create-react-app client
* Then, i installed the concurrently and nodemon dependencies which the react app relies on using: npm install concurrently --save-dev ,and then: npm install concurrently --save-dev.
* Then, I edited the scripts portion of the package.json file to include important entries for the reach side of the app:
  
![Screenshot from 2023-07-10 15-18-32](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/0c595bde-7ee2-4559-9789-6bef2f4f182c)

*Then, I edited the package.json file under clients directory to include the following key- value pair: "proxy": "http://localhost:5000", this makes accesing from the browser easier.
*Then, I ran the following command to launch the app to start running on localhost:3000: npm run dev

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/f02cca8f-3948-4fc6-b666-1dcaf09af593)

*Then I edited the security group of my running instance to permit inbound traffic to port 3000 from the internet.
*Then I created 3 component files in a components directory under ~/Todo/client/src/, namely: Input.js  ListTodo.js  Todo.js.
*Then I edited the Index.js file as below:

![Screenshot from 2023-07-10 15-36-52](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/06c3d107-b101-4ece-8ed6-f3d546c4abf5)

*Then, I did a cd back into the clients directory to install axios: npm install axios
*THen, I edited the ListTodo.js file under components directory:

![Screenshot from 2023-07-10 15-49-23](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/34a42991-6fb8-4bac-ace4-3cc1f817c1cb)

*Then, I edited the Todo.js file under components directory:

![Screenshot from 2023-07-10 15-51-46](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/a69393a8-10c9-4c44-87a7-7eaa94e57ed9)

*Then i edited the App.js, App.css and index.css files under the src directory:

![Screenshot from 2023-07-10 15-54-46](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/06f183da-d2f8-4316-b0c8-9d82f0e8a1f0)

![Screenshot from 2023-07-10 15-56-47](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/6c72e2d1-6064-4f3a-ad2e-01456405780c)

![Screenshot from 2023-07-10 15-59-19](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/80935f80-fb41-42c0-8b8f-35113649c037)

* Then, I did cd into the Todo directory and ran the following command: npm run dev
  
![Screenshot from 2023-07-10 16-02-38](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/9ca15913-7980-401a-b958-8018a6f23f43)
  
* Then, I tried accessing over the internet to confirm that the App has been fully deployed:
  
![Screenshot from 2023-07-10 16-05-44](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/18c304b4-a60c-47e0-a218-86d6d36450d6)





