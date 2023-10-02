![Screenshot from 2023-10-02 19-30-21](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/2a93f812-81cd-41b6-a529-329152b080c0)## ANSIBLE CONFIGURATION MANAGEMENT – AUTOMATING PROJECT 7 TO 10

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

### STEP 5- SETTING UP ANSIBLE INVENTARY AND CREATING A COMMON PLAYBOOK

- Firstly, I ensured that The Jenkins-Ansible host has ssh access to other hosts by generating a public key with command:

 ssh-keygen

-Then, I copied the key inside ~/.ssh/id_rsa.pub file.
-Then, I pasted the contenet in ~/.ssh/authorized_keys file for every other host
-Then, I tested the connection from the Jenkins_Ansible instance by running:

ssh ubuntu@ip_address (for ubuntu based servers)
ssh ec2-user@ip_address (for rhel)

-Then, I edited the dev.yml file under inventory to add the following:

![Screenshot from 2023-10-02 16-52-19](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/912b1529-f370-464c-ada3-4d27a8090cec)


  -Then, I updated the playbooks/common.yml file with following code:

  ![Screenshot from 2023-10-02 17-36-04](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/ee006e99-4d03-418e-ab14-178517c509ac)

#### N/B: This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.

### Step 6– UPDATING GIT WITH THE LATEST CHANGES

- To update git with latest changes, I ran the following:

  git add --all

  git commit -m "commit message"

- After setting personal access token(classic), i did a git push with command

  git push origin dev

- When prompted for password, I pasted the classic token and my push was succesful

![Screenshot from 2023-10-02 19-06-15](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/cd4977c6-ae02-453c-b641-f21f44af2d35)

- Then I created a pull request and merged succesfully to main branch on github
- Back in my terminal, I switched to main branch and did a git pull so that the merge done on github would reflect locally
  
![Screenshot from 2023-10-02 19-22-55](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/3160dada-7227-4d1b-886a-c25098f8d7df)

- Jenkins automatically built the change upon merging as shown below

  ![Screenshot from 2023-10-02 19-25-25](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/2f5a58dd-55c4-4d9f-b9c7-70ac966b61f6)

- All relevant files were automatically saved (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.

![Uploading Screenshot from 2023-10-02 19-30-21.png…]()

### STEP 7- RUNNING FIRST ANSIBLE PLAYBOOK

- I changed directory into ansible-config-mgt folder
  
  cd ansible-config-mgt

- Then, I ran  the following ansible command

  ansible-playbook -i inventory/dev.yml playbooks/common.yml

![Screenshot from 2023-10-02 20-36-29](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/dda27ced-0129-433e-a0be-e193f66a0c9b)

- To verify that the target servers were affected

![Screenshot from 2023-10-02 20-48-17](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/d459aff1-be0a-4672-99ca-5f174e775d38)




