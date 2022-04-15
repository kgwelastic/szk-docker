<img src="https://welastic.pl/wp-content/uploads/2021/10/logo-black.svg" alt="Welastic logo" width="100" align="left">
<br><br>
<br><br>
<br><br>

# Using docker compose tool

## LAB Overview

#### This lab leads you through the steps to prepare two container application.

## Task 1: Login into EC2 machine

1. Follow the steps provided by instructor

1. Run this command to download the current stable release of Docker Compose:
```she
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
2. Apply executable permissions to the binary:
```she
$ sudo chmod +x /usr/local/bin/docker-compose
```

3. Test the installation
```she
$ docker-compose --version
docker-compose version 1.29.2, build 5becea4c
```

## Task 2: Create and run application

1. Create a **"docker-compose.yml"** file with the content below:
```she
version: '3.3'

services:
   db:
     image: mysql:latest
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: admin
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8080:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: admin
       WORDPRESS_DB_PASSWORD: wordpress

volumes:
    db_data:
```
2. Start all the environment in detached mode:
```she
 $ docker-compose up -d
```
3. Wait until docker complete to start the containers, meanwhile let's inspect the wordpress logs:
```she
$ docker-compose logs -f wordpress
```
4. Check the running containers:
```she
$ docker-compose ps
```
5. Browse to the wordpress portal: http://176.34.207.206:8080/
```she
$ curl ifconfig.me
```
6. Enter to the mysql container:
```she
$ docker-compose exec db bin/bash
```

7. Print the os type and environment variable **"MYSQL_USER"** (passed in the yaml file):
```she
# cat /etc/issue
# echo $MYSQL_USER
```
8. Exit from the container
```
# exit
```
## Task 3: Update configuration

1. Update the mysql image and change kind to oracle in the **docker-compose.yml** file to "8.0.28-oracle":
```
image: mysql:8.0.28-oracle
```
```
db:
     image: mysql:8.0.28-oracle
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: admin
       MYSQL_PASSWORD: wordpress
```

 2. Apply the changes to the running environment:

```
$ docker-compose up -d
```
3. Enter to the mysql container:
```she
$ docker-compose exec db bin/bash
```

4. Print the os type and environment variable **"MYSQL_USER"** (passed in the yaml file):
```she
# cat /etc/issue
# cat /etc/redhat-release
# echo $MYSQL_USER
```
5. Exit from the container
```
# exit
```
## Task 4: Cleanup enviroment

1. Let's clean our environment (remove the containers defined in the docker-compose file):
```
$ docker-compose down
```

2. Note that volumes are not deleted by default, so you can recreate the environment at any time

```
$ docker volume ls
```

3. To remove the volumes as well you can use "docker-compose down --volume"

## END LAB

<br><br>

<p align="right">&copy; 2022 Welastic Sp. z o.o.<p>
