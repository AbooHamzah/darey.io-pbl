## ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)

### Step 1 – JENKINS JOB ENHANCEMENT

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
