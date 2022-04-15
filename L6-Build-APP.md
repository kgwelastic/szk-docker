<img src="https://welastic.pl/wp-content/uploads/2021/10/logo-black.svg" alt="Welastic logo" width="100" align="left">
<br><br>
<br><br>
<br><br>

# Create Application

## LAB Overview

#### This lab leads you through the steps to prepare application Wordpress

## Task 1: Login into EC2 machine

1. Follow the steps provided by instructor
2. Install docker if it's necessary
```she
sudo yum update -y && sudo yum install docker -y && sudo service docker start && sudo chkconfig docker on && sudo chmod 777 /var/run/docker.sock
```

## Task 2: Install wordpress and mysql db

1. Download latest images to local cache
```she
$ docker pull mysql
$ docker pull wordpress
```
2. Check and remember ipaddr of EC2
```she
$ ifconfig eth0
$ IPADDR=$(ifconfig eth0 | grep "inet " | awk -F " " '{print $2}')
$ echo $IPADDR
```

3. Create mysql volume 
```she
$ docker volume create db_data
```
4. Run mysql container
```she 
docker container run \
--detach \
--name mysql \
-p 33060:3306 \
-e MYSQL_ROOT_PASSWORD=somewordpress \
-e MYSQL_DATABASE=wordpress \
-e MYSQL_USER=admin \
-e MYSQL_PASSWORD=wordpress \
--mount source=db_data,target=/var/lib/mysql \
mysql:latest
```

5. Run wordpress container
```she 
docker container run \
--detach \
--name wordpress \
-p 8080:80 \
-e WORDPRESS_DB_HOST=$IPADDR:33060 \
-e WORDPRESS_DB_USER=admin \
-e WORDPRESS_DB_PASSWORD=wordpress \
wordpress:latest
```
6. Browse to the wordpress portal: http://176.34.207.206:8080/
```she
$ curl ifconfig.me
```

7. Stop Docker mysql container
```she
$ docker ps | grep mysql | awk -F " " '{print $1}' | xargs docker stop
```

8. Refresh your the wordpress portal: http://176.34.207.206:8080/
```she
$ curl ifconfig.me
```

9. Start mysql container
```she
$ docker ps -a | grep mysql | awk -F " " '{print $1}' | xargs docker start
```

10. Refresh your the wordpress portal: http://176.34.207.206:8080/
```she
$ curl ifconfig.me
```
## Task 3: Create wordpress and connect to AWS RDS instance.

1. Check and note AWS RDS `wordpress` instance endpoints and ports.

2. Run Wordpress with aws parameteres, and your ID
```she 
docker container run \
--detach \
--name wordpress-aws \
-p 8081:80 \
-e WORDPRESS_DB_HOST=_AWS_ENDPOINT_:3306 \
-e WORDPRESS_DB_USER=admin \
-e WORDPRESS_DB_PASSWORD=somewordpress \
-e WORDPRESS_DB_NAME=studentX \
wordpress:latest
```

3. Refresh your wordpress web page: http://176.34.207.206:8081/
```she
$ curl ifconfig.me
```

4. Install mysql tools inside container
```she
$ docker exec -it wordpress-aws bash
$ apt-get update && apt-get install mariadb-client -y
```

5. Create database in mysql
```she
$ mysql -h$(echo $WORDPRESS_DB_HOST | awk -F ":" '{print $1}') -u${WORDPRESS_DB_USER} -p${WORDPRESS_DB_PASSWORD}
MySQL [(none)]> create database studentX;
Query OK, 1 row affected (0.010 sec)

MySQL [(none)]> exit
Bye
$ exit
```

6. Browse to the wordpress portal: http://176.34.207.206:8081/
```she
$ curl ifconfig.me
