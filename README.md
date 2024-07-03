![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![.Net](https://img.shields.io/badge/.NET-5C2D91?style=for-the-badge&logo=.net&logoColor=white)
![C#](https://img.shields.io/badge/c%23-%23239120.svg?style=for-the-badge&logo=csharp&logoColor=white)

By Selman Karaosmanoglu 

## Date created
3 July 2024

# C# .NET 6 Docker Deployment on AWS

.NET 6 on AWS for Containers using Cloud9

## Architecture

```mermaid
flowchart TD;
    A[AWS Cloud9] -->|Docker push| B[Amazon Elastic Container Registry];
    B -->|Deploy| C[AWS App Runner];
    D[Cloud Shell] -->|curl| C;
```

## Setting Up AWS Cloud9

### Create AWS Cloud9 Environment

![alt text](resources/1-cloud9.png)

### Modify Cloud9 Volume

Go to the running EC2 instance of Cloud9 

![alt text](resources/2-volume.png)

Modify volume size to 60GB

![alt text](resources/3-volume-size.png)

### Reboot Cloud9

```bash
sudo reboot
```

![alt text](resources/4-reboot.png)

### Control the new size of the volume

```bash
df --human-readable
```

![alt text](resources/5-control-size.png)

### Install .NET 6 from Microsoft

```bash
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
sudo yum install dotnet-sdk-6.0
sudo yum install aspnetcore-runtime-6.0
sudo yum install dotnet-runtime-6.0
```

### Run dotnet

```bash
cd WebServiceAWS

dotnet run
```

![alt text](resources/6-run-dotnet.png)


### Test with curl

```bash
curl http://localhost:5262

curl http://localhost:5262/hello/aws
```

![alt text](resources/7-curl.png)

### Create Amazon Elastic Container Registry

![alt text](resources/8-ecr.png)

View and copy push commands

![alt text](resources/9-ecr.png)

![alt text](resources/10-ecr.png)

### Build Docker Image

The Dockerfile is as follows:

```bash
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src
COPY ["WebServiceAWS.csproj", "./"]
RUN dotnet restore "WebServiceAWS.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "WebServiceAWS.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "WebServiceAWS.csproj" -c Release -o /app/publish

FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 8080
ENV ASPNETCORE_URLS=http://+:8080

WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "WebServiceAWS.dll"]
```

Run the below command to build docker image from Dockerfile.

```bash
docker build . -t web-service-dotnet:latest
```

![alt text](resources/11-docker.png)

### Apply push commands

```bash
docker tag ..

docker push ..
```

![alt text](resources/12-docker-push.png)

### Create App Runner service

![alt text](resources/13-app-runner.png)

Configure App Runner

![alt text](resources/14-app-runner.png)

Successfull deploy screen

![alt text](resources/15-app-runner.png)

### Control from AWS cloud shell

![alt text](resources/16-cloud-shell.png)

## Reference

* Duke University - Virtualization, Docker, Kubernetes Data Engineering Program
