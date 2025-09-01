# Flow-2: Create a new Docker Image, Run as Container and Push to Docker Hub

## Pre-requisite Step
- Create your Docker hub account. 
- https://hub.docker.com/
- **Important Note**: In the below listed commands wherever you see **stacksimplify** you can replace with your docker hub account id. 


## Step-1: Run the base Nginx container
- Access the URL http://localhost
```
docker run --name mynginxdefault -p 80:80 -d nginx
docker ps
docker stop mynginxdefault
```

## Step-2: Create Dockerfile
- **Dockerfile**
```
FROM nginx
# Custom Labels
LABEL maintainer="Sandeep"
LABEL version="1.0"
LABEL description="A simple nginx application"
COPY index.html /usr/share/nginx/html # both Dockerfile and index.html file should be in the same directory.

ADD staticfiles.tar.gz /usr/share/nginx/html
```

## Step-3: Build Docker Image & run it
```
Dockerfile: It is a simple text file(Dockerfile) that contains set of instructions to build docker image.
docker build -t mynginx-custom:v1 . # . represetns the current directly where Dockerfile presents
docker images
docker run --name mynginx -p 8080:80 -d mynginx-custom:v1 # Run docker container
docker tag mynginx-custom:v1 stacksimplify/mynginx-custom:v1 # Tag the docker image
docker push stacksimplify/mynginx-custom:v1 # Push the image to docker hub
# check in docker hub repo if the image is pushed.
docker search nginx # Search docker image in hub
docker search --fileter=stars=50 nginx
docker search --filter=is-official=true nginx
```
## Dockerfile - Label Instruction
- Adds metadata to an image
- An image can have more than one label
- Labels included in the base image are inherited by your custom image

```

How to verify image labels
docker image inspect --format='{{json.Config.Labels}}' myimage
```

## Dockerfile - COPY vs ADD
- COPY: Copies new files or directories from source path and adds them to file system of image. Files and directories cab be copied from the build context, build stage, named context or image.
- ADD: Copies new files or directories from source path and adds them to file system of image. Files and directories cab be copied from the build context, remote URL and GIT repo.
    - if we will ADD a tar or zip file, then this command will automatically unzip and add.
