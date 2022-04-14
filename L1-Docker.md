<img src="https://welastic.pl/wp-content/uploads/2021/10/logo-black.svg" alt="Welastic logo" width="100" align="left">
<br><br>
<br><br>
<br><br>

# Install Docker

## LAB Overview

#### This lab leads you through the steps to prepare enviroment.

## Task 1: Login into EC2 machine

1. Follow the steps provided by instructor

## Task 2: Install Docker.

In thisk task you will install Docker on EC2 machines.

1. In your EC2 machine run following commands. 

```she
sudo yum install docker
```

```she
sudo service docker start
```

```she
sudo chkconfig docker on
```

```she
sudo chmod 777 /var/run/docker.sock
```

2. Download application from Docker Hub repository

```she
docker pull kaminskypavel/mario
```

3. Run download application

```she
docker run --detach -p 8080:8080 --name mario kaminskypavel/mario
```

4. Check your IP, and open in the Web Browser to validate if application is available eq: http://176.34.207.206:8080/
```she
$ curl ifconfig.me
```

## END LAB

<br><br>

<p align="right">&copy; 2022 Welastic Sp. z o.o.<p>