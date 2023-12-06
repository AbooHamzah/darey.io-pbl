# ANSIBLE DYNAMIC ASSIGNMENTS (INCLUDE) AND COMMUNITY ROLES
#

In this project, we introduced the concept of the use of Dynamic assignments. The major difference between static and dynamic assignments is in the use of `import and include ststements.`

Include does similar thing as Imports but differs because it is dynamic. Dynamic in the sense that ansible is able pick changes in playbooks added to a master playbook in real time. In import ansible preprocesses everything runs the playbook with the data it has. Changes made while executing the playbook is ignored.

Created a role-feature branch to implement the dynamic assignments  of ansible

![1 branch](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/e616f37e-a658-47c3-a18b-320995fbc702)



Set up environment vars which contains variables perculiar to each environment

![2 env_vars](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/19441534-539d-4142-ba35-3df4c56fece7)




Apply the following configuration to the env_vars.yaml file in the dynamic ssignments folder

![3 envVars](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/1fa566a0-edc1-4d5a-abf5-9bba1aa84923)


Using ansible galaxy, installing configuration roles for apache and nginx. This configurations have been pre-written and referenced inside our static-assignments.

![4 edits](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/be17ec8b-0742-4ac8-a171-8d016e1daec0)



Running ansible-playbook on entry-point site.yml which loops through env_vars.yaml and conditionally applies configurations based on specified variables in each environment.

![5 completed](https://github.com/AbooHamzah/darey.io-pbl/assets/108676700/9a763373-398b-4ecd-ae8f-ddcee3097d43)

