<img src="https://welastic.pl/wp-content/uploads/2021/10/logo-black.svg" alt="Welastic logo" width="100" align="left">
<br><br>
<br><br>
<br><br>

# Using docker-compose

## LAB Overview

In this lab we'll take a look at some examples docker-compose mssql + aspnetcore.

## Task 1: Login into EC2 machine

1. Follow the steps provided by instructor

2. Install git tools.
```she
sudo yum install git -y
```

## Task 2: Package and run a .net custom app using docker-compose

1. Clone the example dotnet repository
```she
$ git clone https://github.com/tometchy/Docker-compose-dotnet-core-and-mssql.git
```

2. Enter example repo:
```she
$ cd Docker-compose-dotnet-core-and-mssql/
```

3. Inspect the docker-compose.yml file:
```she
$ cat docker-compose.yml
```
4. Inspect the database server:
```she
$ cat Db/Dockerfile
```
5. Inspect the application:
```she
$ cat Aspnetcoreapp/Dockerfile
```
6. Start all the environment in detached mode:
```she
 $ docker-compose up -d
```
7. Wait until docker complete to start the containers, meanwhile let's inspect the mssql database logs:
```she
$ docker-compose logs -f db
```
8. Inspect the application logs:
```she
$ docker-compose logs -f app
```
9. Check the running containers:
```she
$ docker-compose ps
```
10. Browse to the wordpress portal: http://176.34.207.206:8080/
```she
$ curl ifconfig.me
```
11. Enter to the mysql container:
```she
$ docker-compose exec db bin/bash
```
## Task 2: Cleanup enviroment

1. Let's clean our environment (remove the containers defined in the docker-compose file):
```
$ docker-compose down
```

## END LAB

<br><br>

<p align="right">&copy; 2022 Welastic Sp. z o.o.<p>
