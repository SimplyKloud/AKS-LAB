# Flow-1: Pull Docker Image from Docker Hub and Run it

## Step-1: Verify Docker version and also login to Docker Hub
```
docker version
docker login
SimplyKloud / Dotcom12@@1
```

## Step-2: Pull Image from Docker Hub
```
docker pull stacksimplify/dockerintro-springboot-helloworld-rest-api:1.0.0-RELEASE
```

## Step-3: Run the downloaded Docker Image & Access the Application
- Copy the docker image name from Docker Hub
```
docker run --name app1 -p 80:8080 -d stacksimplify/dockerintro-springboot-helloworld-rest-api:1.0.0-RELEASE
http://localhost/hello

# For Mac with Apple Chips (use different application)
Step-1: Install Docker with Apple Chips binary (https://docs.docker.com/desktop/mac/install/) on your mac machine

Step-2: Run the simple Nginx Application container. 
docker run --name kube1 -p 80:80 --platform linux/amd64 -d  stacksimplify/kubenginx:1.0.0
http://localhost

## Sample Output
kalyanreddy@Kalyans-Mac-mini-2 ~ % docker run --name kube1 -p 80:80 --platform linux/amd64 -d  stacksimplify/kubenginx:1.0.0
370f238d97556813a4978572d24983d6aaf80d4300828a57f27cda3d3d8f0fec
kalyanreddy@Kalyans-Mac-mini-2 ~ % curl http://localhost
<!DOCTYPE html>
<html>
   <body style="background-color:lightgoldenrodyellow;">
      <h1>Welcome to Stack Simplify</h1>
      <p>Kubernetes Fundamentals Demo</p>
      <p>Application Version: V1</p>
   </body>
</html>%
kalyanreddy@Kalyans-Mac-mini-2 ~ % 

docker images
docker rmi <image_name> # Remove image
docker pull stacksimplify/mynginx:v1
docker run --name <container_name> -p <Host_Port>:<Container_Port> -d <image_name>:<tag> #-d means run container in background with detached mode
docker run --name mycontainer -p 8080:80 -d stacksimplify/mynginx:v1 #localhost:8080
```

## Step-4: List Running Containers
```
docker ps # lists the running containers
docker ps -a # lists all containers including stopped ones
docker ps -q # lists only container ids

# connect to docker contaienr
docker exec -it <container_name> /bin/bash
host # container id
ls # list the files inside contaienr
exit # come out of contaienr
docker exec -it <container_name> ls # directly connect and execute command in a single command
docker inspect <container_name or id>

docker stop <container_name or id> # Stop the container
docker start <container_name or id> # start the container
docker rm <container_name or id> # remove the container and container should be in stopped state
```

