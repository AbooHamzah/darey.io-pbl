## ANSIBLE DYNAMIC ASSIGNMENTS (INCLUDE) AND COMMUNITY ROLES

### STEP 1- INTRODUCING DYNAMIC ASSIGNMENT INTO OUR STRUCTURE
- In terminal(VSC), I started a new branch and called it dynamic-assignments.

git checkout -b dynamic-assignments
  
![Screenshot from 2023-10-18 19-53-20](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/fe9881f0-85f4-4c99-9a0f-a14adccd0ce8)

- Then, I created a new folder, named it dynamic-assignments.

cd /git/ansible-config-mgt/
  
- Then inside this folder, I created a new file and name it env-vars.yml.

touch env-vars.yml

- Then, I edited the env-vars.yml file to add the following:

![Screenshot from 2023-10-18 20-57-32](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/f888ab6c-8f6e-4242-b4e0-9471fab212a9)

### STEP 2- UPDATE SITE.YML WITH DYNAMIC ASSIGNMENTS
- I update site.yml file with the following to make use of the dynamic assignment.
 
![Screenshot from 2023-10-18 21-15-18](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/097cbe7a-c9eb-4351-b478-26ed186e3c81)

- Then, I created a new git branch named "roles-feature" and switched to it
- Then, I did a cd into roles directory and downloaded a new MySQL role  ansible-galaxy by running command

install geerlingguy.mysql and rename the folder to mysql

- Then, I renamed the downloaded folder

mv geerlingguy.mysql/ mysql

- Then i went into the just renamed mysql/defaults/main.yml and edited the database credentials

  ![Screenshot from 2023-10-22 17-34-51](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/4c4a9977-12fb-4da7-be93-0097e6f3fa22)

- Then I uploaded the changes to github

git add --all

git commit -m "Commit new role files into GitHub"

git push --set-upstream origin roles-feature

- Then i merged this new created github branch with main


  ### STEP 3- CREATING LOAD BALANCER ROLES

  
