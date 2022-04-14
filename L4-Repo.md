<img src="https://welastic.pl/wp-content/uploads/2021/10/logo-black.svg" alt="Welastic logo" width="100" align="left">
<br><br>
<br><br>
<br><br>

# Create Docker Repository

## LAB Overview

#### This lab leads you through the steps to prepare enviroment.

## Task 1: Login into EC2 machine

1. Follow the steps provided by instructor

## Task 2: Create ECR repository.

1. In comand bellow replace X with your student number, and run it to create ECR repository.
```she
aws ecr create-repository --repository-name studentX --region eu-west-1
```
2. Go back to AWS Console, in **Services** click **Elastic Container Service**.
3. In the left menu click **Repositories**
4. Click on your repository name. 
5. Click on **View push commands**. 
6. To retrieve an authentication token and authenticate your Docker client to your registry. Paste first command in your EC2 terminal. 
```she
$ aws ecr get-login-password --region eu-west-1 | docker login --username AWS --password-stdin 536966783968.dkr.ecr.eu-west-1.amazonaws.com
WARNING! Your password will be stored unencrypted in /home/ec2-user/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

```
7. Go back to AWS console.
8. To push image to repository you have to tag it. Paste third comand in your EC2 terminal and rename name of docker image as in example bellow. 
```she
$ docker tag aspnetapp:latest 536966783968.dkr.ecr.eu-west-1.amazonaws.com/studentX:latest
```
9. Copy fourth command to your EC2 terminal, and run this command to push your docker image to AWS ECR repostory. 
```she
$ docker push 536966783968.dkr.ecr.eu-west-1.amazonaws.com/studentX:latest
```
10. Go back to AWS console and check if your image is present in ECR repository. 

11. To push image to repository you have to tag it. Paste third comand in your EC2 terminal and rename name of docker image as in example bellow. 
```she
$ docker tag kaminskypavel/mario 536966783968.dkr.ecr.eu-west-1.amazonaws.com/studentX:mario
```
12. Copy fourth command to your EC2 terminal, and run this command to push your docker image to AWS ECR repostory. 
```she
$ docker push 536966783968.dkr.ecr.eu-west-1.amazonaws.com/studentX:mario
```

13. Go back to AWS console and check if your image is present in ECR repository. 
```she
docker run --detach -p 8081:8080 536966783968.dkr.ecr.eu-west-1.amazonaws.com/studentX:mario
```
14. Check your IP, and open in the Web Browser to validate if application is available eq: http://176.34.207.206:8081/
```she
$ curl ifconfig.me
```

## END LAB

<br><br>

<p align="right">&copy; 2022 Welastic Sp. z o.o.<p>