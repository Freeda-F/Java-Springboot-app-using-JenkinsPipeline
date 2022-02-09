# Spring Boot Application deployed with Jenkinspipeline

In this demo, we will create a scripted pipeline for the springboot application based on the Jenkinsfile. Using this pipeline, we will build a docker image and deploy the application to a remote development environment.

GitHub repo  for springboot application : https://github.com/Freeda-F/hello-world-spring-boot

## Features

On every pipeline execution, the code goes through the following steps:

1. Code is cloned from Github into the Jenkins server.
2. Docker image for spring-boot application is build using Dockerfile present in repository.
3. Push the docker image to docker hub.
4. Deploy a containerized application in a remote development environment from the docker image pushed to the dockerhub.

## Prerequisites

- Install Jenkins on the master machine. (Once installed Jenkins can be accessed using http://<public-Ip>:8080)
```
amazon-linux-extras install epel -y
yum install java-1.8.0-openjdk-devel -y
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
yum -y install jenkins
systemctl start jenkins
systemctl enable jenkins
```
- Install Docker and git in the Master machine as well.
- You need to have a DokcerHub account and a GitHub account.
- An Ec2 instance spinned up for devlopment environment. [Docker should be install in this remote server]

## Pipeline Architecture
  
![image](https://user-images.githubusercontent.com/93197553/153228734-33aa0a81-d38c-47dc-a9b4-b7f142406a07.png)

## Pipeline Overview

- Stage 1 : SCM Checkout
  
  Code is cloned from github repository to Master machine. It includes the code for springboot application, Dockerfile and a container-check.sh script which is used in stage 4 & 5
  
- Stage 2 : Build Docker image
  
  Docker image is build using the Dockerfile in the master machine.
  
- Stage 3 : Push Docker image to DockerHub.
  
  The Docker image which was built in the previous stage is pushed into dockerhub after logging into the dockerhub account.
  
- Stage 4 : Copying the container-check.sh script to dev environment.
  
  The container-check.sh script is copied to the remote dev environment using scp command. This script checks if the docker container with the name 'springboot-app' (name of the docker container) exists and if it exists the container is removed and is replaced by a new container.
  
- Stage 5 : Deploy container on dev environment
  
  The docker container is deployed to the dev environment using the container-check.sh script. 
  
  
## Variables Used

The variables are given in the Jenkinsfile using withEnv([])  

```
IMAGE_NAME=freedafrancis/java-springboot-app:v2
GIT_URL=https://github.com/Freeda-F/hello-world-spring-boot 
```
 
## Provisioning
  
#### Jenkins Configuration

1. Login to Jenkins (http://ip:8080/) and install the required plugins.

Manage Jenkins --> Manage Plugins and Search for ansible plugin in the available section and install the same.
  
![image](https://user-images.githubusercontent.com/93197553/150861966-91eb05e8-dcb2-4246-af13-a74ef4e2ea4a.png)

 2. Create a Pipeline project from Dashboard and paste the Jenkinsfile under the pipeline section.
 
 ![image](https://user-images.githubusercontent.com/93197553/153234998-08e616f0-f7be-4d36-a610-a627231b0b92.png)
  
 3. Also, make sure to configure the required credentials i.e Dockerhub credentials and Private-key file to connect to remte server under the credentials section.
 
  ![image](https://user-images.githubusercontent.com/93197553/153235639-c3dbfb36-3bf4-423c-a7b0-576649441b77.png)

4. Now, you may click on 'Build Now' option to run the project. 

### Result
![image](https://user-images.githubusercontent.com/93197553/153219985-bebade12-c1a0-40f6-a1f8-305bd39a3587.png)


![image](https://user-images.githubusercontent.com/93197553/153220108-067db205-9f60-4779-87e2-1479dc7c85c9.png)


