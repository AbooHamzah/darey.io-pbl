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

- Then, I saved the configuration and build the job.

  ![Screenshot from 2023-09-30 18-40-59](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/ad3a5126-7a22-46ca-b1a5-75e7b4992477)

