<img src="https://welastic.pl/wp-content/uploads/2021/10/logo-black.svg" alt="Welastic logo" width="100" align="left">
<br><br>
<br><br>
<br><br>

# Build Docker Commands

## LAB Overview

In this lab we'll take a look at some build Docker commands.

## Task 1: Login into EC2 machine

1. Follow the steps provided by instructor

## Task 2: Package and run a .net custom app using Docker

1. Install git tools.
```she
$ cd ~
$ sudo yum install git -y
```

2. Clone the example dotnet repository
```she
$ git clone https://github.com/dotnet/dotnet-docker
Cloning into 'dotnet-docker'...
<output truncated>
Resolving deltas: 100% (29030/29030), done.
```

3. Go to the aspnetapp example
```she
$ cd dotnet-docker/samples/aspnetapp
```

4. Build docker base on DocerFile

```she
$ docker build -t aspnetapp .
Sending build context to Docker daemon  8.258MB
<output truncated>
Successfully built 6bf0381518f7
Successfully tagged aspnetapp:latest
```

```she
$ cat Dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:6.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

```she
$ docker run --detach -p 8080:80 --name aspnetcore_sample2 aspnetapp
5529f4dacc78091bfc204e6180ef79708db409c65cf415345d1197e4aaba8970
```

```she
$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                   NAMES
5529f4dacc78   aspnetapp      "dotnet aspnetapp.dll"   14 seconds ago   Up 13 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   aspnetcore_sample2
94e30cc96f84   mysql:latest   "docker-entrypoint.s…"   57 minutes ago   Up 57 minutes   3306/tcp, 33060/tcp                     mydb
```

Check your IP, and open in the WebBrowser eq: http://176.34.207.206:8080/
```she
$ curl ifconfig.me
```
