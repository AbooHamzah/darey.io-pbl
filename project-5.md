## IMPLEMENTING A CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS).

### STEP 0- CONFIGURING ENVIRONMENT:
- I set up two ASW EC2 instances giving each appropriate names:

![Screenshot from 2023-07-23 16-38-59](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/9d9b3d64-2727-4082-a56c-c773b86ecda4)

- Then, I did an SSH into both EC2 instances and did an upgrade for both using the command:
  sudo apt update -y
  
### STEP 1- SETTING UP MYSQL CLIENT AND SERVER:
- I installed sql server on the server instance and sql client on the client instance repectively using the commands:

  sudo apt install mysql-server -y (on server instance)

  sudo apt install mysql-client -y (on client server)

![Screenshot from 2023-07-23 17-00-12](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/aa92f4ed-21bc-4eff-abe1-21a94eeb934f)

- Then, I edited the security group on the server instance to allow inbound TCP traffic from  
the public ip addr. of the client server:

![Screenshot from 2023-07-23 17-34-23](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/31ae1aa9-22f9-4a4b-b6c3-f1643fcdd48d)

- Then, I edited the mysql configuration file to change the binding address:
  
    sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf

![Screenshot from 2023-07-23 17-39-45](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/6141f8fc-24fe-43b7-a6a4-31d5c65682a4)

- To make the change in binding address take effect, I made sure to restart the mysql server by running the following command:

  sudo systemctl restart mysql 

### STEP 2- SETTING CREDENTIALS, CREATING USER AND DATABASE ON MYSQL SERVER:
- On the mysql server, I created a user "akeem" then I created a database "daredb" and linked the user with the database using the commands:

  sudo mysql -p

  USER 'akeem'@'%' IDENTIFIED WITH mysql_native_password BY (password);

  CREATE DATABASE dareydb;

  GRANT ALL ON dareydb.* TO 'akeem'@'%' WITH GRANT OPTION;

  FLUSH PRIVILEGES;

![Screenshot from 2023-07-23 18-14-24](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/67171159-dca5-45d0-af3d-87336e9dd78a)

- Then, to confirm that the darebd database was created and the akeem user was created, i then the following commands on mysqlserver:

  show databases;
  
  select user,host from mysql.user;

![Screenshot from 2023-07-23 18-18-27](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/2342bd3c-d044-4006-ab19-5d9fdce88a29)

### STEP 3- CONNECTING TO THE MYSQL SERVER FROM THE MYSQL CLIENT USING MYSQL UTILITY:
- Now back on mysql client server, I ran the following linux syntax to remotely access the mysql server:

  sudo mysql -u akeem -h (mysql server private ip) -p
  
- Then I was prompted to enter a password and i entered the password attached with user 'akeem'.
- Then, I confirmed that I could list databases attached to  user 'akeem' on the server from the client by running the command:

  show databases;

![Screenshot from 2023-07-23 18-37-49](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/5d6576f1-87b7-41e0-bb20-20301701b957)



