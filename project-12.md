## ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)

### STEP 1 – JENKINS JOB ENHANCEMENT

- I did an SSH into the Jenkins-Ansible server and created a new directory called ansible-config-artifact – where I intended to store all artifacts after each build.

sudo mkdir /home/ubuntu/ansible-config-artifact

- Then, I changed permissions to this directory, so Jenkins would be able to save files there.

 chmod -R 777 /home/ubuntu/ansible-config-artifact

 - Then I went to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab, I searched for Copy Artifact and install this plugin without restarting Jenkins

 - Then, I created a new Freestyle project and named it save_artifacts.
 - Then, I configured this project to be triggered upon successful build of the "ansible" project.

![Screenshot from 2023-10-04 20-13-55](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/aa0119aa-bb83-4729-b923-b446aa93eb15)

![Screenshot from 2023-10-04 20-14-17](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/b93f847a-537c-44f4-9967-846d329907f6)

![Screenshot from 2023-10-04 20-14-31](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/89baf181-75f0-47ff-8b69-633c4021c668)

- Then, I tested the set-up by making some change in README.MD file inside ansible-config-mgt repository (right inside master branch).

### N/B: if you get an error, check permissions, in this case 755

![Screenshot from 2023-10-04 21-16-54](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/fecfa49e-4520-47ea-afd9-206c9f00761c)


![Screenshot from 2023-10-04 21-27-08](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/5c9c2a23-7a97-43e6-b942-99120e75a749)

### STEP 2 - REFACTORING ANSIBLE CODE BY IMPORTING OTHER PLAYBOOKS INTO SITE.YML

- I created another folder in my git root repository named "static-assignments"
- Then, I did a cd into playbooks and created site.yml (site.yml will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed. Including common.yml that I created previously)
- Then i moved common.yml from playbooks to static assignments directory
- In site.yml, I imported common.yml

![Screenshot from 2023-10-07 14-57-47](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/4cc9125c-5ec6-4f25-9f7a-7b7abe9189a4)

- The folder structure thus became as depicted below:

![Screenshot from 2023-10-07 14-58-40](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/b397268e-a5f2-4cba-b1e0-cc1124caf038)

- Then, in order to test that the imports in site.yml work, I created another playbook under static-assignments and named it common-del, the aim of this playbook was to uninstall the already deleted wireshark on all dev server(web1, 2, lb, db,nfs).
- Below is the content of the common-del playbook

![Screenshot from 2023-10-07 15-32-47](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/7fe18ea3-391d-4628-9879-62a8381b4310)

- Then I edited the site.yml file under playbooks as below:

![Screenshot from 2023-10-07 15-41-07](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/a3b694d4-c480-4d70-8926-b80daf528be0)

- Then, I ran the playbook with the below command:

  cd /home/ubuntu/ansible-config-mgt/

  ansible-playbook -i inventory/dev.yml playbooks/site.yml

![Screenshot from 2023-10-07 15-58-04](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/7ce40aa3-78da-44db-b1eb-437360e71309)

![Screenshot from 2023-10-07 16-10-55](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/64b8cc04-2cf1-40f7-9a67-3c6093fc925f)


### STEP 3 - CONFIGURE UAT WEBSERVERS WITH A ROLE ‘WEBSERVER’.

- I spun up 2 new Ec2 instances named "WEB1-UAT" and "WEB2-UAT" with RHEL-9 AMI and t2.micro as instance type.
- Then, I used an Ansible utility called ansible-galaxy inside ansible-config-mgt/roles directory
  
mkdir roles

cd roles

ansible-galaxy init webserver

![Screenshot from 2023-10-07 17-16-49](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/48e2f2b5-dc0a-4824-803f-b4491a44356d)

![Screenshot from 2023-10-07 17-46-27](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/d4c348ad-0670-4bbd-84bc-1a9825548e63)

-After removing unnecessary folders and files:

![Screenshot from 2023-10-07 17-46-48](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/755f51a2-d183-4f13-be8c-8513473687f8)

-Then, I updated the UAT inventory ansible-config-mgt/inventory/uat.yml file with IP addresses of the 2 UAT Web servers

![Screenshot from 2023-10-07 17-49-52](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/1f432193-0e69-474a-a95a-5bf74743f941)

- Then I configured SSH-Agent
- Then in  /etc/ansible/ansible.cfg file, I uncommented roles_path string and provided a full path to your roles directory

  roles_path    = /home/ubuntu/git/ansible-config-mgt/roles, so Ansible could know where to find configured roles.

- Then, I did a cd into tasks directory, and within the main.yml file, start writing configuration tasks to do the following:

Install and configure Apache (httpd service)
Clone Tooling website from GitHub https://github.com/<your-name>/tooling.git.
Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.
Make sure httpd service is started

![Screenshot from 2023-10-07 21-28-21](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/1602ed59-1eba-403b-8aae-702faf2eb273)


### STEP 4- REFERENCE WEBSERVER ROLE

- Within the static-assignments folder, I created a new assignment for uat-webservers uat-webservers.yml. N/B: This is where I referenced the role.

![Screenshot from 2023-10-07 21-35-03](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/14de0da9-496d-48b6-9f7b-d3caa0132892)

- Then, I modified the site.yml file to reference uat-webserver.yml

![Screenshot from 2023-10-07 21-37-20](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/5f568e78-e962-46b0-bec9-f9f344dfdd2c)

### Step 5 – COMMITING AND TESTING

- I commited the changes, create a Pull Request and merged them to master branch, made sure the jobs were triggered on jenkins and files copied.
- Then I ran the playbook against the uat inventory 

ansible-playbook -i /home/ubuntu/git/ansible-config-mgt/inventory/uat.yml /home/ubuntu/git/ansible-config-mgt/playbooks/site.yml

![Screenshot from 2023-10-08 19-55-19](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/9590ec6f-0601-4722-9f24-e5ad5a0729a2)

- Then I tried reaching both uat servers over the browser:

  http://Web1-UAT-Server-Public-IP-or-Public-DNS-Name/index.php

  ![Screenshot from 2023-10-08 20-01-57](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/f06b6d62-20b6-4afc-8afa-81ac5fa17545)

![Screenshot from 2023-10-08 20-06-09](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/65a7aaed-8a80-4ca7-a85b-f3b541d5300e)


- Thus our ansible architecture now looks like this:

![Screenshot from 2023-10-08 20-07-46](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/31f5016e-4c2f-430a-891d-65089b80ed70)

