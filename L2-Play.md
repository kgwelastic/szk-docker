<img src="https://welastic.pl/wp-content/uploads/2021/10/logo-black.svg" alt="Welastic logo" width="100" align="left">
<br><br>
<br><br>
<br><br>

# Basic Docker Commands

## LAB Overview

In this lab we'll take a look at some basic Docker commands.

## Task 1: Login into EC2 machine

1. Follow the steps provided by instructor

## Task 2: Run a single-task Alpine Linux container

In this Task we're going to start a new container and tell it to run the `hostname` command. The container will start, execute the `hostname` command, then exit.

1. Run the following command in your Linux console:

```she
$ docker container run alpine hostname
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
df9b9388f04a: Pull complete
Digest: sha256:4edbd2beb5f78b1014028f4fbb99f3237d9561100b6881aabbf5acce2c4f9454
Status: Downloaded newer image for alpine:latest
```

The output above shows that the `alpine:latest` image could not be found locally. When this happens, Docker automatically *pulls* it form Docker Hub.

After the image is pulled, the container's hostname is displayed (`df9b9388f04a` in the example above).

2. Docker keeps a container running as long as the process it started inside the container is still running. In this case, the `hostname` process completes when the output is written, so the container exits. The Docker platform doesn't delete resources by default, so the container still exists in the `Exited` state.

    List all containers

    ```she
    $ docker container ls --all
    CONTAINER IDIMAGE       COMMAND     CREATED     STATUS    PORTS       NAMES
    df9b9388f04aalpine      "hostname"  50 seconds ago      Exited (0) 49 seconds ago       
    ```

    Notice that your Alpine Linux container is in the `Exited` state.

    > **Note:** The container ID *is* the hostname that the container displayed. In the example above it's `df9b9388f04a`

Containers which do one task and then exit can be very useful. You could build a Docker image that executes a script to configure something. Anyone can execute that task just by running the container - they don't need the actual scripts or configuration information.


## Task 3: Run an interactive Ubuntu container

You can run a container based on a different version of Linux than is running on your Docker host.

In the next example, we are going to run an Ubuntu Linux container

1. Run a Docker container and access its shell.

    In this case we're giving the `docker container run` command three parameters:

    * `--interactive` says you want an interactive session
    * `--tty` allocates a psuedo-tty
    * `--rm` tells Docker to go ahead and remove the container when it's done executing

    The first two parameters allow you to interact with the Docker container.

    We're also telling the container to run `bash` as its main process (PID 1).

    ```she
    $ docker container run --interactive --tty --rm ubuntu bash
    ```

2. Run some commands in the container:

    - `ls /` - lists the contents of the root directory
    - `ps aux` - shows all running processes in the container.
    - `cat /etc/issue` - shows which Linux distro the container is running, in this case Ubuntu 16.04 LTS

3. Type `exit` to leave the shell session. This will terminate the `bash` process, causing your container to exit.

    > **Note:** As we used the `--rm` flag when we started the container, Docker removed that container when it stopped. This means if you run another `docker container ls --all` you won't see the Ubuntu container.

4. For fun, let's check the version of our host VM

```she
$ cat /etc/issue
\S
Kernel \r on an \m
```

## Task 4: Run a background MySQL container

Background containers are how you'll run most applications. Here's a simple example using MySQL.

1. Let's run MySQL in the background container using the `--detach` flag. We'll also use the `--name` flag to name the running container `mydb`.

    We'll also use an environment variable (`-e`) to set the root password (NOTE: This should never be done in production):

```she
$ docker container run \
--detach \
--name mydb \
-e MYSQL_ROOT_PASSWORD=haslo123 \
mysql:latest

Unable to find image 'mysql:latest' locally
latest: Pulling from library/mysql
f003217c5aae: Pull complete
...
4607fa685ac6: Pull complete
Digest: sha256:1c75ba7716c6f73fc106dacedfdcf13f934ea8c161c8b3b3e4618bcd5fbcf195
Status: Downloaded newer image for mysql:latest
94e30cc96f84c7d2ed8e9d5a3873657384b1c631a99ef3e1eb00f1ca24a8fe91
```
Once again, the image we requested was not available locally, so Docker pulled it from Docker Hub.
As long as the MySQL process is running, Docker will keep the container running in the background.

2. List running containers

```she
$ docker container ls
CONTAINER ID   IMAGE  COMMAND  CREATED  STATUS  PORTS NAMES
94e30cc96f84   mysql:latest   "docker-entrypoint.s…"   26 seconds ago   Up 24 seconds   3306/tcp, 33060/tcp   mydb
```

Notice your container is running

3. You can check what's happening in your containers by using a couple of built-in Docker commands: `docker container logs` and `docker container top`

```she
$ docker container logs mydb
<output truncated>
2022-01-01T00:01:01.941696Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2022-04-13T18:57:31.960846Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
2022-04-13T18:57:31.960954Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.28'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
```

This shows the logs from your Docker container.

Let's look at the running processes inside the container.

```she
$ docker container top mydb
PID USERTIMECOMMAND
2876999 0:00 mysqld
```
    
You should see the MySQL demon (`mysqld`) is running. Note that the PID shown here is the PID for this process on your docker host. To see the same `mysqld` process running as the main process of the container (PID 1) try:
	
```she
$ docker container exec mydb ps -ef
OCI runtime exec failed: exec failed: container_linux.go:380: starting container process caused: exec: "ps": executable file not found in $PATH: unknown
```
You received error because ps proces is not install in the container, now you will add pstools via procps debian package

```she
$ docker container exec mydb /usr/bin/apt-get -y update
$ docker container exec mydb /usr/bin/apt-get -y install procps
```

Now you should see that process is running

```she
$ docker container exec mydb ps -ef
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
libstor+            5266                5241                0                   18:57               ?                   00:00:01            mysqld
```

Although MySQL is running, it is isolated within the container because no network ports have been published to the host. Network traffic cannot reach containers from the host unless ports are explicitly published.

4. List the MySQL version using `docker container exec`.

`docker container exec` allows you to run a command inside a container. In this example, we'll use `docker container exec` to run the command-line equivalent of `mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version` inside our MySQL container.

```she
$ docker container exec -it mydb \
mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version

mysql: [Warning] Using a password on the command line interface can be insecure.
mysql  Ver 8.0.28 for Linux on x86_64 (MySQL Community Server - GPL)
```

The output above shows the MySQL version number, as well as a handy warning.

5. You can also use `docker container exec` to connect to a new shell process inside an already-running container. Executing the command below will give you an interactive shell (`sh`) in your MySQL container.  

```she
$ docker exec -it mydb sh
#
```

Notice that your shell prompt has changed. This is because your shell is now connected to the `sh` process running inside of your container.

6. Let's check the version number by running the same command we passed to the container in the previous step.

```she
# mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version

mysql: [Warning] Using a password on the command line interface can be insecure.
mysql  Ver 8.0.28 for Linux on x86_64 (MySQL Community Server - GPL)
```
Notice the output is the same as before.

8. Let's check the databases list in this mysql instance.

```she
# mysql --user=root --password=$MYSQL_ROOT_PASSWORD
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.28 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)

mysql> quit;
Bye
```

9. Type `exit` to leave the interactive shell session.

Your container will still be running. This is because the `docker container exec` command started a new `sh` process. When you typed `exit`, you exited the `sh` process and left the `mysqld` process still running.

## END LAB


<br><br>

<p align="right">&copy; 2022 Welastic Sp. z o.o.<p>
