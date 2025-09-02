##  Reference - https://github.com/stacksimplify/docker-in-a-weekend/blob/main/08-Dockerfile-RUN-EXPOSE/Dockerfiles/Dockerfile

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
docker build -t mynginx-custom:v1 . # . represetns the current directly where Dockerfile presents. Provide the image name with version no
docker images
docker run --name mynginx -p 8080:80 -d mynginx-custom:v1 # Run docker container
docker tag mynginx-custom:v1 stacksimplify/mynginx-custom:v1 # Tag the docker image
docker push stacksimplify/mynginx-custom:v1 # Push the image to docker hub. provide the docker login username/image name with version no
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
 
## ADD fetch from URL
- Create a new Git Public Repo
- Upload some files and commit
- Create a Git Release
	- Create a release by clicking on right side bar >> Releases >> Create a new release
	- Create a new tag
	- Give some release version 1.o.o
	- Click on Publish release
- In the Dockerfile, add the step
	- ADD https://github.com/..... /usr/share/nginx/html
- Build the image and run the container

## Dockerfile ARG instruction
- Defines a variable that user can pass at build time to the builder with
	- docker command: docker build
	- Using the flag: --build-arg <var_name>=value
- We can define one or more ARG instructions.
- ENV variables always overrides ARG variables if same variables defined in both places.
- ARG variables are available during the build time of the image. ENV variables are available during runtime of the container.
- Build and run the below dockerfile

```
Dockerfile
---------------
ARG NGINX_VERSION=1.26

FROM nginx:${NGINX_VERSION}-alpine-slim # Ensure image name should be in same pattern or format in the regitry

# Custom Labels
LABEL maintainer="Sandeep"
LABEL version="1.0"
LABEL description="A simple nginx application"

COPY index.html /usr/share/nginx/html # both Dockerfile and index.html file should be in the same directory.
-----------------------------------------------------------------------
After docker build and run, verify if the files are there.
docker exec -it <container_name> ls  /usr/share/nginx/html

To verify the version as per our ARG, run the below command
docker exec -it <container_name> nginx -v
```

## Pass ARG at build time
```
docker build --build-arg NGINX_VERSION=1.27 -t [Image_name]:[Image_Tag] . # . is required or the path is required where the Dockerfile is present to build the image
```

## Dockerfile RUN instruction
- The RUN instruction will execute any commands to create a new layer of top of the current image.
- The added layter is used in the next step in Dockerfile.
  ### Cache invalidation for RUN instruction

## EXPOSE instruction
- Informs docker that the container listens on the specified network ports at runtime.
- You can specify whether the port listens or TCP or UDP, and the default is TCP if you dont specify a protocol.
  - EXPOSE 80: defaults to TCP and exposes on TCP
  - EXPOSE 80/UDP
  - EXPOSE 80/TCP

 ```
# Use nginx:alpine-slim as base Docker Image
FROM nginx:alpine-slim

# OCI Labels
LABEL org.opencontainers.image.authors="Kalyan Reddy Daida"
LABEL org.opencontainers.image.title="Demo: Using RUN, EXPOSE Instructions in Dockerfile"
LABEL org.opencontainers.image.description="A Dockerfile demo illustrating the usage of RUN and EXPOSE instructions"
LABEL org.opencontainers.image.version="1.0"


# Copy all Nginx configuration files from nginx-conf directory
COPY nginx-conf/*.conf /etc/nginx/conf.d/

# Copy all HTML files from nginx-html directory
COPY nginx-html/*.html /usr/share/nginx/html/

# Install curl using RUN
RUN apk --no-cache add curl

# Expose the ports 8081, 8082, 8083 (default port 80 already exposed from base nginx image)
EXPOSE 8081 8082 8083
```
## ENV instruction
- ENV sets the environment variables.
- ENV persisted in the final image and available in the container when it is run from this image
- e.g. ENV NAME="SANDEEP"

## WORKDIR instruction
- Sets the working directory for any RUN, COPY, CMD, ENTRYPOINT and ADD instructions that follow it in the Dockerfile
- If WORKDIR not spectified, then "/" is the default directory
- To avoid unintended operations in unknown directories, best practice is always set WORKDIR instructions

  ## CMD instruction
  - Defines the command to run when starting a container from the image
  - Only one CMD instructin is allowed in a Dockerfile. If there are multiple, then the last CMD is used
  - CMD ["Param1","Param2"]
  - CMD ["Executable","Param1","Param2"]
  - **Can be overriden using docker run **
```
requirements.txt
--------------
Flask==3.0.3

app.py
--------------
from flask import Flask, render_template
import os

app = Flask(__name__)

# Get the environment variable APP_ENVIRONMENT (default to 'dev')
environment = os.getenv('APP_ENVIRONMENT', 'dev')

@app.route('/')
def home():
    # Serve different templates based on environment
    if environment == 'dev':
        return render_template('dev.html')
    elif environment == 'qa':
        return render_template('qa.html')
    else:
        return "<h1>Unknown Environment</h1>"

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)

Dockerfile
--------------
# Use python:3.12-alpine as the base image
FROM python:3.12-alpine

# OCI Labels
LABEL org.opencontainers.image.authors="Kalyan Reddy Daida"
LABEL org.opencontainers.image.title="Demo: ARG vs ENV in Docker"
LABEL org.opencontainers.image.description="A Dockerfile demo illustrating the difference between ARG (build-time) and ENV (runtime) instructions"
LABEL org.opencontainers.image.version="1.0"

# Define build-time argument for environment (defaults to "dev")
ARG ENVIRONMENT=dev

# Set the ENV variable using the ARG value
ENV APP_ENVIRONMENT=${ENVIRONMENT}

# Set the working directory inside the container
WORKDIR /app

# Copy requirements and install dependencies
COPY requirements.txt requirements.txt

# Install packages from requirements.txt
RUN pip install -r requirements.txt

# Copy the application code
COPY app.py .

# Copy the templates directory
COPY templates/ ./templates/

# Print the environment for demo purposes
RUN echo "Building for environment: ${APP_ENVIRONMENT}"

# Start the Flask app
CMD ["python", "app.py"]

Templates Folder
------------
Dev.html
------------
<!DOCTYPE html> 
<html> 
  <body style='background-color:rgb(152, 202, 134);'> 
    <h1>Welcome to StackSimplify - ARG (Build-time) and ENV (Runtime) Variables</h1> 
    <h2>Environment: DEV</h2> 
    <p>Learn technology through practical, real-world demos.</p> 
    <p>Application Version: V1</p>     
  </body>
</html>

Qa.html
-----------
<!DOCTYPE html> 
<html> 
  <body style='background-color:rgb(152, 202, 134);'> 
    <h1>Welcome to StackSimplify - ARG (Build-time) and ENV (Runtime) Variables</h1> 
    <h2>Environment: QA</h2> 
    <p>Learn technology through practical, real-world demos.</p> 
    <p>Application Version: V1</p>     
  </body>
</html>
```

```
# Change to the directory containing your Dockerfile
cd Dockerfiles

# Build Docker Image using default ENVIRONMENT (dev)
docker build -t demo9-arg-vs-env:v1 .

# Run Docker Container
docker run --name my-arg-env-demo1-dev -p 8080:80 -d demo9-arg-vs-env:v1

# List Docker Containers
docker ps

# Print environment variables from Container
docker exec -it my-arg-env-demo1-dev env | grep APP_ENVIRONMENT

# Expected Output:
# APP_ENVIRONMENT=dev

# Access the application in your browser
http://localhost:8080
```

## Run Docker Container and Override ENV Variable
```
# Run Docker Container and override APP_ENVIRONMENT to 'qa'
docker run --name my-arg-env-demo2-qa -p 8081:80 -e APP_ENVIRONMENT=qa -d demo9-arg-vs-env:v1

# List Docker Containers
docker ps

# Print environment variables from Container
docker exec -it my-arg-env-demo2-qa env | grep APP_ENVIRONMENT

# Expected Output:
# APP_ENVIRONMENT=qa

# Access the application in your browser
http://localhost:8081


# List files in the working directory inside the container
docker exec -it my-arg-env-demo1-dev ls /app

# Expected Output:
# app.py
# requirements.txt
# templates

# Inspect the Docker image to verify CMD instruction
docker image inspect demo9-arg-vs-env:v1 --format='{{.Config.Cmd}}'

# Expected Output:
# [python app.py]
```

## Setting Default Environment to QA Without Changing Dockerfile
- Question: How do you ensure the default environment is qa when building the Docker image without changing the Dockerfile?

- Answer:

- You can override the ENVIRONMENT build-time argument during the image build process using the --build-arg flag. This allows you to set the default APP_ENVIRONMENT to qa in the image without modifying the Dockerfile.
```
# Build Docker Image with ENVIRONMENT set to 'qa'
docker build --build-arg ENVIRONMENT=qa -t demo9-arg-vs-env:v1-qa .

# Run Docker Container without specifying the environment variable
docker run --name my-arg-env-demo3-qa -p 8082:80 -d demo9-arg-vs-env:v1-qa

# List Docker Containers
docker ps

# Print environment variables from Container
docker exec -it my-arg-env-demo3-qa env | grep APP_ENVIRONMENT


# Expected Output:
# APP_ENVIRONMENT=qa

# Access the application in your browser
http://localhost:8082
```

## Override CMD at the time of docker run
```
1. create a Dockerfile
2. create one html file

build the image and run the container withour passing any parameter
docker run --name my-cmd-demo1 -p 8080:80 -d demo10-dockerfile-cmd:v1
# This will start the container with Nginx. We can access in browser

# Run Docker Container by overriding the CMD instruction
docker run --name my-cmd-demo2 -it demo10-dockerfile-cmd:v1 /bin/sh
# Nginx will not run because CMD is overridden with /bin/sh

# You can start Nginx manually if desired:
nginx -g 'daemon off;'
```

<img width="1080" height="538" alt="image" src="https://github.com/user-attachments/assets/76e71d97-838e-4954-8460-6dd24bb7b104" />


