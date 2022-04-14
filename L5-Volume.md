<img src="https://welastic.pl/wp-content/uploads/2021/10/logo-black.svg" alt="Welastic logo" width="100" align="left">
<br><br>
<br><br>
<br><br>

# Create Docker Volume

## LAB Overview

#### This lab leads you through the steps to prepare (non)persistent volumes.

## Task 1: Login into EC2 machine

1. Follow the steps provided by instructor

## Task 2: Create and use Volume `webvolume`.
0. Login as root
```she
$ sudo su -
```
1. Create volume `webvolume`.
```she
$ docker volume create webvolume
```
2. List available volumes
```she
$ docker volume ls
DRIVER    VOLUME NAME
local     e41334c1b40dee51c7b574c6b7c03ee350a079e0dc8efe6ee4a0c45a98fac96f
local     webvolume
```
3. Inspect volume
```she
$ docker volume inspect webvolume
[
    {
        "CreatedAt": "2022-01-01T21:36:56Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/webvolume/_data",
        "Name": "webvolume",
        "Options": {},
        "Scope": "local"
    }
]
```
4. Create file in the persistent volume
```she
$ sudo echo "<html></h1>Hello World</h1></html>" > /var/lib/docker/volumes/webvolume/_data/index.html
```
5. Run latest version on ngnix with alredy created volume
```she
$ docker run -d -p 8080:80 \
  --name ngnixvolume \
  --mount source=webvolume,target=/usr/share/nginx/html \
  nginx:latest
```
6. Enter to the container (additional task find your container ID)
```she
$ docker exec -it a35cdf395dd5 /bin/bash
```
7. Double check the file content
```she
$ cat /usr/share/nginx/html/index.html
```
9. Check your IP, and open in the Web Browser eq: http://176.34.207.206:8080/

10. Remove volume, if you receive error please try solve the issue by yourself or ask instructor :)
```she
$ docker volume rm webvolume
```

## Task 3: Bind folder into container

1. Create custom folder containing index.html.
```she
$ mkdir ~/bindfolder
$ cd bindfolder
$ echo "<html></h1>Bind folder into container :)</h1></html>" > ~/bindfolder/index.html
$ pwd
/root/bindfolder
```

2. Bind folder instead of Volume
```she
$ docker run -d -p 8080:80 \
  --name ngnixvolume \
  --mount type=bind,source=/root/bindfolder,target=/usr/share/nginx/html \
  nginx:latest
```
3. Check your IP, and open in the Web Browser to validate if application is available eq: http://176.34.207.206:8080/
```she
$ curl ifconfig.me
```

## END LAB

<br><br>

<p align="right">&copy; 2022 Welastic Sp. z o.o.<p>
