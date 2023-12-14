## Project 

### The context
                
The objective of this project is to deploy the battleboat application by creating a complete DevSecOps.



Infrastructure

  We need 4 servers
  
 - A main server Jenkins monitor the agent
 - A build server to build, tests, scan  docker images and deploy it, 
 - Sonarqube server: Static Code Analysis
 - preproduction server 
 - production server


 ## Tools
   
- Cloud: AWS
- Configuration management: Ansible
- Infrastructure as code: Terraform
- Container Engine: Docker
- Source Code Management: Github
- Scheduling: Jenkins
- Security: clair, Sonarqube
- Notification: Slack


## Description

* This is a complete pipeline CI/CD with jenkins, ansible  to deploy a python application 
* The differents stages are :
1. Ensure lint syntax of diferents langages (bash, yaml) is OK
2. Analyze the code with sonarqube
3. Ensure syntax ansible is OK with ansible-lint
4. Build image on the build server, scan it with clair and push docker image  on the dockerhub
5. Deploy application on the preproduction and production server
6. Ensure application is deployed

by Bertille Matchum (carolledevops@yahoo.com)

 











### Technical word

Docker, docker-compose, Ansible, Tags, Playbooks, Roles, Galaxy, Jenkins, Shared-library, Pipeline, Notification, linter, DevSecOps, Clair, Sonarqube

### Reference repository

+ [Source code development](https://github.com/carollebertille/CI-battleboat.git "Source code development")
+ [Shared-library](https://github.com/carollebertille/shared-library.git "Shared-library")
  
