## ANSIBLE CONFIGURATION MANAGEMENT – AUTOMATING PROJECT 7 TO 10

### STEP 1-INSTALLING AND CONFIGURING ANSIBLE ON EC2 INSTANCE

- I updated the name tag on the Jenkins EC2 Instance to Jenkins-Ansible.
- Then, I created a new repository in GitHub and named it ansible-config-mgt.
- Back in the EC2 terminal, i updated my apt-cache and installed ansible by running the following:
  
  sudo apt update

  sudo apt install ansible
- Then, I verified that Ansible was installed by checking Ansible version by running:

   ansible --version
  
  ![Screenshot from 2023-09-30 16-07-29](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/4cac8f7d-f34d-40b9-8807-87efcedb7785)

### STEP 2- CONFIGURING JENKINS BUILD JOB TO SAVE THE REPOSITORY CONTENT ANYTIME IT IS ALTERED.

- I created a new Freestyle project named ansible in Jenkins and pointed it to the ‘ansible-config-mgt’ repository.

  ![Screenshot from 2023-09-30 18-33-48](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/89ce9c40-a99f-4b41-96cf-04acc860c214)

  ![Screenshot from 2023-09-30 18-35-11](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/40a557d7-b280-4137-8c6e-111600414d01)

- Then, I configured Webhook in GitHub and set webhook to trigger ansible build.

  ![Screenshot from 2023-09-30 18-45-56](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/e3041482-724f-4993-8f47-48706b392dbb)

- Then, I configured a Post-build job to save all (**) files.

![Screenshot from 2023-09-30 18-35-29](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/e8788f63-64c2-4161-b909-4f56d75826cf)

![Screenshot from 2023-09-30 18-36-03](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/3a5b6b0a-9560-4261-b749-a104de8e0407)

![Screenshot from 2023-09-30 18-39-43](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/d9278a6e-7593-4a30-969f-fee84d6571eb)

- Then, I saved the configuration and built the job.

  ![Screenshot from 2023-09-30 18-40-59](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/ad3a5126-7a22-46ca-b1a5-75e7b4992477)

### STEP 3- PREPARING IDE (VISUAL STUDIO CODE)

- From visual studio, I opened terminal and did an SSH connection to out JENKINS-ANSIBLE.

- Then, from the JENKINS-ANSIBLE instance, I configured it to connect to the created GitHub repository - ansible-config-mgt.git and then I cloned the ansible-config-mgt repo with command:
  
git clone (ansible-config-mgt repo link)

![Screenshot from 2023-10-02 12-19-34](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/32d78ae5-3764-4d55-844b-36796299d332)

### STEP 4- ANSIBLE DEVELOPMENT

- In the ansible-config-mgt GitHub repository, I created a new branch for development of a new feature.

git branch dev

- Then, I checkedout to the dev branch

git checkout dev

- Then i created two folders, namely:  playbooks(it will be used to store all playbook files.) and inventory(it will be used to keep hosts organised.)

![Screenshot from 2023-10-02 12-55-06](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/54c26285-f19e-4a18-8632-235b95a16cb0)

- in the playbooks folder, I created a file named common.yml

  touch common.yml

- In the inventory folder, I created four files: prod.yml, staging.yml, dev.yml, uat.yml

![Screenshot from 2023-10-02 13-03-31](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/1e29cb11-bfc0-4877-ba63-c5adc48c02ac)

### STEP 5- SETTING UP ANSIBLE INVENTARY

- Firstly, I ensured that The Jenkins-Ansible host has ssh access to other hosts by generating a public key with command:

 ssh-keygen

-Then, I copied the key inside ~/.ssh/id_rsa.pub file.
-Then, I pasted the contenet in ~/.ssh/authorized_keys file for every other host
-Then, I tested the connection from the Jenkins_Ansible instance by running:

ssh ubuntu@ip_address (for ubuntu based servers)
ssh ec2-user@ip_address (for rhel)

-Then, I edited the dev.yml file under inventory to add the following:

![Screenshot from 2023-10-02 16-52-19](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/912b1529-f370-464c-ada3-4d27a8090cec)

- Then I did a git add and git commit.
- 
