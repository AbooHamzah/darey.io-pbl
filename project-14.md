# CI/CD PIPELINE FOR A PHP BASED APPLICATION

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/93452dd5-f764-41fb-91a5-ee0d5c87e1a2)


## Project Description:

In this project, I set up a CI/CD Pipeline for a PHP based application. Above is thw architecture of the CI/CD process.

This project is architected in two major repositories with each repository containing its own CI/CD pipeline written in a Jenkinsfile:
- **ansible-config-mgt REPO**: this repository contains JenkinsFile which is responsible for setting up and configuring infrastructure required to carry out processes required for our application to run. It does this through the use of ansible roles. **<u>This repo is infrastructure specific</u>**
- **PHP-todo REPO** : this repository contains jenkinsfile which is focused on processes which are <u>**application build specific**</u> such as building, linting, static code analysis, push to artifact repository etc

## Prerequisites

I made use of AWS virtual machines for this, using 6 servers for the project which included:
**Nginx Server**: This would act as the reverse proxy server to our site and tool. <br/>

**Jenkins server**: To be used to implement your CI/CD workflows or pipelines. Select a t2.medium at least, Ubuntu 20.04 and Security group should be open to port 8080 <br/>

**SonarQube server**: To be used for Code quality analysis. Select a t2.medium at least, Ubuntu 20.04 and Security group should be open to port 9000 <br/>

**Artifactory server**: To be used as the binary repository where the outcome of your build process is stored. Select a t2.medium at least and Security group should be open to port 8081 <br/>

**Database server**: To server as the databse server for the Todo application <br/>

**Todo webserver**: To host the Todo web application. <br/>
#

## Environments
Ansible Inventory should look like this

```
├── ci
├── dev
├── pentest
├── pre-prod
├── prod
├── sit
└── uat
```
#

## ANSIBLE ROLES FOR CI ENVIRONMENT
I automated the setup of `SonarQube` and `JFROG Artifactory`, we used `ansible-galaxy` to install this configuration into our ansible roles which were used to run against the `sonarqube server and artifactory server`.


## Configuring Ansible For Jenkins Deployment
#

We created a Jenkins-server with a t2.medium specification because we needed more compute power to run builds compared to the jenkins-server we have been using since project 13

### Prepare your Jenkins server
I connected to the Jenkins instance on VScod via SSH and set up SSH-agent to ensure ansible get the private jey required to connect to all other servers:

```
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```

### Installation of the following packages and dependencies on the server:
- I installed git : sudo apt install git 

- Then, I cloned down the Asible-config-mgt repository: git clone https://github.com/AbooHamzah/ansible-config-mgt.git

- Then, I installed Jenkins and its dependencies. Steps to install Jenkins can be found here
 
- Configure Ansible For Jenkins Deployment. 
- Navigate to Jenkins URL: <Jenkins-server-public-IP>:8080

In the Jenkins dashboard, I clicked on Manage Jenkins -> Manage plugins and search for Blue Ocean plugin. Install and open Blue Ocean plugin.

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/9fd45c38-1330-42e6-9537-ba4b7af0632a)


- Acquired personal access token from github
 
![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/776a7505-c94b-4403-aedc-7c9be3586bcb)


![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/3228a9e1-a7e3-4cb8-8236-3ffae1197b9a)

- This job got created automatically by blue-ocean after connection with github repo.
  
![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/2fc28a34-eea5-4286-976b-d93a78daebc8)

#

## Creating JENKINSFILE
- In Vscode, inside the Ansible project, I created a new directory and named it deploy, created a new file Jenkinsfile inside the directory.
  
![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/d9d1e01f-1dd6-4c54-8e35-fd2011cedc10)


- Added the code snippet below to start building the Jenkinsfile gradually. This pipeline initially had just one stage called Build and the only thing we were doing was using the shell script module to echo Building Stage

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/bf387790-437f-4cbc-baab-e1be43a61565)


- Specified buildpath to help jenkins locate jenkinsfile
  
![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/5c29a228-032f-4c4d-a9be-31d7ec2ab0fd)


![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/f4345e1b-a3c7-4430-b3bb-c9394585e69b)


So Blue Ocean set up a multibranched pipeline after the initial connection with github. A multibranched pipeline is one which contains multiple branches depending on the github repository.

- To test this out we created a new branch 
  
![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/5300f5ab-afac-4a45-a608-533493b6b0a6)


I added some more code to the jenkinsfile of the new branch which contained a TEST build job and ran it.

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/456f8a28-2be9-42bf-a235-1f3dad6a6555)

#

## RUNNING ANSIBLE PLAYBOOK FROM JENKINS
#

I installed Ansible on Jenkins Jenkins-Ansible Server. <br/>

I installed Ansible plugin in Jenkins UI <br/>

I created Jenkinsfile from scratch. Note: Ensure that Ansible runs against the Dev environment successfully. <br/>

I added the following code to the jenkinsfile

```
pipeline {
  agent any

  environment {
      ANSIBLE_CONFIG="${WORKSPACE}/deploy/ansible.cfg"
    }

  stages {
      stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }

      stage('Checkout SCM') {
         steps{
            git branch: 'main', url: 'https://github.com/Micah-Shallom/ansible-config-mgt.git'
         }
       }

      stage('Prepare Ansible For Execution') {
        steps {
          sh 'echo ${WORKSPACE}' 
          sh 'sed -i "3 a roles_path=${WORKSPACE}/roles" ${WORKSPACE}/deploy/ansible.cfg'  
        }
     }

      stage('Run Ansible playbook') {
        steps {
           ansiblePlaybook become: true, credentialsId: 'private-key', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory/dev, playbook: 'playbooks/site.yml'
         }
      }

      stage('Clean Workspace after build') {
        steps{
          cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenUnstable: true, deleteDirs: true)
        }
      }
   }

}
```
Some possible errors I watched out for:

- I ensured that the git module in Jenkinsfile is checking out SCM to main branch instead of master (GitHub has discontinued the use of Master)

- Jenkins needs to export the ANSIBLE_CONFIG environment variable. I put the .ansible.cfg file alongside Jenkinsfile in the deploy directory. This way, anyone would easily identify that everything in there relates to deployment. Then, using the Pipeline Syntax tool in Ansible, I generated the syntax to create environment variables to set. Then, I entered this into the ancible.cfg file

```
timeout = 160
callback_whitelist = profile_tasks
log_path=~/ansible.log
host_key_checking = False
gathering = smart
ansible_python_interpreter=/usr/bin/python3
allow_world_readable_tmpfiles=true


[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=30m -o ControlPath=/tmp/ansible-ssh-%h-%p-%r -o ServerAliveInterval=60 -o ServerAliveCountMax=60 -o ForwardAgent=yes
```
- Remember that ansible.cfg must be exported to environment variable so that Ansible knows where to find **Roles**. But because you will possibly run Jenkins from different git branches, the location of Ansible roles will change. Therefore, you must handle this dynamically. You can use Linux Stream Editor sed to update the section roles_path each time there is an execution. You may not have this issue if you run only from the main branch.

- If you push new changes to Git so that Jenkins failure can be fixed. You might observe that your change may sometimes have no effect. Even though your change is the actual fix required. This can be because Jenkins did not download the latest code from GitHub. Ensure that you start the Jenkinsfile with a clean up step to always delete the previous workspace before running a new one. Sometimes you might need to login to the Jenkins Linux server to verify the files in the workspace to confirm that what you are actually expecting is there. Otherwise, you can spend hours trying to figure out why Jenkins is still failing, when you have pushed up possible changes to fix the error.

- Another possible reason for Jenkins failure sometimes, is because you have indicated in the Jenkinsfile to check out the main git branch, and you are running a pipeline from another branch. So, always verify by logging onto the Jenkins box to check the workspace, and run git branch command to confirm that the branch you are expecting is there.
- Parameterizing Jenkinsfile For Ansible Deployment. So far we have been deploying to dev environment, what if we need to deploy to other environments? We will use parameterization so that at the point of execution, the appropriate values are applied. To parameterize Jenkinsfile For Ansible Deployment, Update CI inventory with new servers
```
[tooling]
<SIT-Tooling-Web-Server-Private-IP-Address>

[todo]
<SIT-Todo-Web-Server-Private-IP-Address>

[nginx]
<SIT-Nginx-Private-IP-Address>

[db:vars]
ansible_user=ec2-user
ansible_python_interpreter=/usr/bin/python

[db]
<SIT-DB-Server-Private-IP-Address>
```
5. I updated Jenkinsfile to introduce parameterization. Below is just one parameter. It had a default value in case no value is specified at execution. It also has a description so that everyone is aware of its purpose.
```
pipeline {
    agent any

    parameters {
      string(name: 'inventory', defaultValue: 'dev',  description: 'This is the inventory file for the environment to deploy configuration')
    }
...
```
6. In the Ansible execution section of the Jenkinsfile, I removed the hardcoded inventory/dev and replace with `${inventory}`

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/434a9a0e-e9f4-4a8c-b600-a84516f05953)

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/15b3d810-1090-46f2-a7b9-946060c64080)

#

## CI/CD PIPELINE FOR TODO APPLICATION
#
Our goal here was to deploy the Todo application onto servers directly from Artifactory rather than from git.

- I updated Ansible with an Artifactory role. I installed artifactory role from the Ansible galaxy repository. 

- Then, I opened a web browser and inputed the URL https://. I was then redirected to the Jfrog Atrifactory page. Entered default username and password: admin/password. Once in create username and password and create your new repository. (Take note of the reopsitory name)

On our jenkins server, I installd git and then pulled our php-todo application into our server
```
https://github.com/darey-devops/php-todo.git
```
I installed PHP and other packages

```
yum module reset php -y
yum module enable php:remi-7.4 -y
yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
systemctl start php-fpm
systemctl enable php-fpm
```
![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/6a031a17-4613-41ab-8c52-2e8e44306f74)


On the Jenkins-Ansible server, I installed the PLOT PLUGIN and ARTIFACTORY PLUGIN and set it up. Add the artifactory server IP to the JFROG global configuration

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/eadd5a8d-e6fa-4a2d-9086-a910cc76ceee)


Then I ran the jenkinsfile to trigger ansible playbook to setup artifactory on the artifactory server
![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/bb6e10d0-46dd-464c-ab0d-e6b150c902d9)


Then I opend port in artifactory security group

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/4796aa80-a6aa-4691-980e-30d25cff0c51)


![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/71c4c96f-1154-4829-90e6-50c33d851600)

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/d706d425-8b6e-4546-ac89-1867a1aa82aa)


Then I created a GENERIC Repository called ABOOHAMZAH. This will be used to store our build artifacts
![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/ec91b305-60cc-4cdf-b8dc-b1cba1675e80)

#

## Integrate Artifactory repository with Jenkins
#

1. In VScode I created a new Jenkinsfile in the php-Todo repository
2. Using Blue Ocean, I created a multibranch Jenkins pipeline
3. I installed mysql client: `sudo apt install mysql -y`
4. Loged in into the DB-server(mysql server) and set the the bind address to 0.0.0.0: sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
5. I created database and user. **NOTE**: The task of setting the database is done by the `MySQL` ansible role
6. I Ran the php-todo pipeline  
   
![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/e8f434dd-f9fb-42c7-93ff-8850dce2be76)

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/6744fc13-098d-4917-8008-ad1d0ec8aaae)


7. Then, I updated Jenkinsfile with proper pipeline configuration. In the Checkout SCM stage, I ensured I specified the branch as main and changed the git repository to mine.
```
pipeline {
    agent any

  stages {

     stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }

    stage('Checkout SCM') {
      steps {
            git branch: 'main', url: 'https://github.com/Micah-Shallom/php-todo.git'
      }
    }

    stage('Prepare Dependencies') {
      steps {
             sh 'mv .env.sample .env'
             sh 'composer install'
             sh 'php artisan migrate'
             sh 'php artisan db:seed'
             sh 'php artisan key:generate'
      }
    }
  }
}
```
When running we got an error. This was due to the fact that the Jenkins Server being the client server could not communicate with the DB server.

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/6c9cabee-93d3-473d-a045-80f07b7a7d2f)


We then installed mysql client on the Jenkins server and configured it.

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/a744c2d4-05d1-4e15-82f7-1a1820258cc1)

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/3949574e-0abe-4934-81cf-8be2622eb8bd)


The DB migration job passed after setting up the MYSQL client on the Jenkins server

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/469a8d26-85d4-445e-aa45-fd39dfa194c0)


Visualization the PHP code analytics using the Jenkins Plot plugin.

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/ac9108cf-61af-4863-bf7e-a57ae3740af2)


![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/84e856cd-fa4f-45cf-a8d9-b4a66c74be20)


8. I bundled the application code into an artifact (archived package) and uploaded to Artifactory
   
- Install Zip: Sudo apt install zip -y
```
stage ('Package Artifact') {
    steps {
            sh 'zip -qr php-todo.zip ${WORKSPACE}/*'
     }
    }
```
9. I published the resulted artifact into Artifactory making sure to specify the target as the name of the artifactory repository I created earlier
```
stage ('Upload Artifact to Artifactory') {
          steps {
            script { 
                 def server = Artifactory.server 'artifactory-server'                 
                 def uploadSpec = """{
                    "files": [
                      {
                       "pattern": "php-todo.zip",
                       "target": "PBL/php-todo",
                       "props": "type=zip;status=ready"

                       }
                    ]
                 }""" 

                 server.upload spec: uploadSpec
               }
            }

        }
```
![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/44c644db-b672-496f-ad8f-507ebe335f1b)

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/d97e4cea-fca9-4a2b-b01e-deb6b264cc98)


I deployed the application to the dev environment by launching Ansible pipeline. I made sure to update the inventory/dev with the Private IP of the TODO-server and the site.yml file is updated with todo play.

```
stage ('Deploy to Dev Environment') {
    steps {
    build job: 'ansible-project/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'dev']], propagate: false, wait: true
    }
  }
```
#
## SONARQUBE INSTALLATION
#

SonarQube is a tool that can be used to create quality gates for software projects, and the ultimate goal is to be able to ship only quality software code.

Despite that DevOps CI/CD pipeline helps with fast software delivery, it is of the same importance to ensure the quality of such delivery. Hence, we will need SonarQube to set up Quality gates. In this project we will use predefined Quality Gates (also known as The Sonar Way). Software testers and developers would normally work with project leads and architects to create custom quality gates.


## Setting Up SonarQube

On the Ansible config management pipeline, execute the ansible playbook script to install sonarqube via a preconfigured sonarqube ansible role.

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/17ebaf81-8882-49b4-81f0-23b8de28fc89)


When the pipeline is complete, access sonarqube from the browser using the `<sonarqube_server_url>:9000/sonar`

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/4144bd01-a55b-47f9-a120-e6312aa57f7b)

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/06a944a9-3bca-4a21-ae9e-ce7e7b3e5813)

#
## CONFIGURE SONARQUBE AND JENKINS FOR QUALITY GATE
#
- Install SonarQube Scanner plugin

- Navigate to configure system in Jenkins. Add SonarQube server: Manage Jenkins > Configure System

- To generate authentication token in SonarQube to to: `User > My Account > Security > Generate Tokens`
 
![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/30eebfd3-9b73-4726-aa2e-30abf81455ae)


- Configure Quality Gate Jenkins Webhook in SonarQube – The URL should point to your Jenkins server http://{JENKINS_HOST}/sonarqube-webhook/ Go to:Administration > Configuration > Webhooks > Create

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/93f89190-9823-4349-80a0-ace11b6acfb9)


- Setup SonarQube scanner from Jenkins – Global Tool Configuration. Go to: Manage Jenkins > Global Tool Configuration

- Update Jenkins Pipeline to include SonarQube scanning and Quality Gate. Making sure to place it before the "package artifact stage" Below is the snippet for a Quality Gate stage in Jenkinsfile.

```
stage('SonarQube Quality Gate') {
    environment {
        scannerHome = tool 'SonarQubeScanner'
    }
    steps {
        withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
        }

    }
}
```
NOTE: The above step will fail because we have not updated sonar-scanner.properties.
- Configure sonar-scanner.properties – From the step above, Jenkins will install the scanner tool on the Linux server. You will need to go into the tools directory on the server to configure the properties file in which SonarQube will require to function during pipeline execution. `cd /var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQubeScanner/conf/.`
- Open sonar-scanner.properties file: `sudo vi sonar-scanner.properties` 
- Add configuration related to php-todo project

```
sonar.host.url=http://<SonarQube-Server-IP-address>:9000
sonar.projectKey=php-todo
#----- Default source code encoding
sonar.sourceEncoding=UTF-8
sonar.php.exclusions=**/vendor/**
sonar.php.coverage.reportPaths=build/logs/clover.xml
sonar.php.tests.reportPath=build/logs/junit.xml 
```
#
## End-to-End Pipeline Overview
#

Conditionally deploy to higher environments
In the real world, developers will work on feature branch in a repository (e.g., GitHub or GitLab). There are other branches that will be used differently to control how software releases are done. You will see such branches as:

Develop
Master or Main
(The * is a place holder for a version number, Jira Ticket name or some description. It can be something like Release-1.0.0)
Feature/*
Release/*
Hotfix/*
etc.

There is a very wide discussion around release strategy, and git branching strategies which in recent years are considered under what is known as GitFlow (Have a read and keep as a bookmark – it is a possible candidate for an interview discussion, so take it seriously!)

Assuming a basic gitflow implementation restricts only the develop branch to deploy code to Integration environment like sit.

Let us update our Jenkinsfile to implement this:

First, we will include a When condition to run Quality Gate whenever the running branch is either develop, hotfix, release, main, or master

```
stage('SonarQube Quality Gate') {
      when { branch pattern: "^develop*|^hotfix*|^release*|^main*", comparator: "REGEXP"}
        environment {
            scannerHome = tool 'SonarQubeScanner'
        }
        steps {
            withSonarQubeEnv('sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner -Dproject.settings=sonar-project.properties"
            }
            timeout(time: 1, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
            }
        }
    }
```

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/a4c3d81a-f180-4625-99c9-15be4499880b)


![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/0dbd6964-cfbd-47c9-b9f9-fe1f78332856)

![image](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/5f4df62e-70e4-499f-936e-87d0a83b1ce3)


