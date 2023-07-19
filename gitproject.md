## GIT PROJECT

### STEP 0 - INITIALIZING A REPOSITORY AND MAKING COMMITS
- After installing git, i created a directory named DevOps, and did a cd into it with commands:
  mkdir DevOps
  cd DevOps
- Then, I initiated git using the command: git init
- Then, I created an index.txt file

![Screenshot from 2023-07-19 15-04-26](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/4456193e-1859-43ee-a9b8-2b88515e9c87)

- Then, I edited the index.txt file.
- Then, I added the changes made to staging and commited the change by running commands:
  git add .
  git commit -m "initial commit"

![Screenshot from 2023-07-19 15-11-09](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/2f87c986-43bb-4587-9af8-002d9f2bef2d)

### STEP 1 - WORKING WITH BRANCHES
- I created a new branch(prod) and switched to it by running the git command:
  git switch -c prod
- Then, I switched back into the master branch using: git switch master.
- Then, I edited the index.txt file adding another line of comment.
- Then, i added to staging and commited the new change with commands:
  git add .
  git commit -m "added a new line to my story"
- Then, I switched to the prod branch with the aim of merging changes in master to prod:
  git switch prod
- Then, I merged master branch to prod branch:
  git merge master

![Screenshot from 2023-07-19 15-42-00](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/ac344837-0e8a-4c4e-bace-d86bf3590b1b)

### STEP 2 - LINKING REMOTE REPOSITORY ON GITHUB WITH LOCAL REPOSITORY
- I logged into my github account and created a new repository
- Then I linked my local with the remote repository by running the commands:
  git remote add origin https://github.com/AbooHamzah/gitproject.git
  git push -u origin master
  
![Screenshot from 2023-07-19 18-39-48](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/84ea2176-d1c1-4718-8028-b9beab40b04d)

![Screenshot from 2023-07-19 18-40-06](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/22010f3c-9741-4b20-800b-5ea3102e3c43)

### STEP 3 - COLLABORATION ON GITHUB
- A friend of mine intends to work on my project with me. I have made available to him my repo url and all he needs to do is to use the following command to get a copy of the repository, so he can contribute:
  git clone https://github.com/AbooHamzah/gitproject.git




  
  

