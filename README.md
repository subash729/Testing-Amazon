# Question 1.
## 1. Make an account in https://hub.docker.com.
   
Answer : 
Account is created successfully
username : ***subash729**
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q1-T1-account-image.jpg">
</p>
## 2. Build a docker image which just prints **hello class** and exits.

Commands used
```
nano Dockerfile
```

Scipts used
```bash
FROM docker.io/ubuntu:3.16.9
CMD ["echo", "Hello class"]
```

### Step -2  Builindg an image
```
sudo docker build -t hello-class .
sudo docker images
```
image pulling 
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q1-T2-ubuntu-linux.jpg">
</p>

## 3. Try to minimize the size of this image as much as you can!

### Step -1 Listing and removing older heavy image
```
sudo docker images
sudo docker rmi edc6bba3fc86
```

### Step - 2 : Creating docker file
Commands used
```
nano Dockerfile
```

Scipts used
```bash
FROM docker.io/alpine:3.16.9
CMD ["echo", "Hello, class!"]
```

### Step -3  Builindg an image
```
sudo docker build -t hello-class .
sudo docker images
```

Note :  older was ``` ~ 77 MB ``` and this older alpine linux is only ``` ~5 MB ``` 
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q1-T3-alpine-linux.jpg">
</p>

## 4. Push this image in dockerhub.
### Step - 1 : Login to Registery
```bash
sudo docker login

Log in with your Docker ID or email address to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com/ to create one.
You can log in with your password or a Personal Access Token (PAT). Using a limited-scope PAT grants better security and is required for organizations using SSO. Learn more at https://docs.docker.com/go/access-tokens/

Username: subash729
Password:

Login Succeeded

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q1-T4-1-login.jpg">
</p>


```
### Step - 2 : Tagging and pushing images to registery
```bash
sudo docker tag hello-class subash729/hello-class:subash-v1
sudo docker push subash729/hello-class:subash-v1
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q1-T4-2-tagging-and-pushing-image.jpg">
</p>

### Step - 3 : Veryfying the images at Registry

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q1-T4-3-image-verification-in-registry.jpg">
</p>


## 5. Write a bash script which builds+tags+pushes the image into the dockerhub. (Bonus: Make the tag increase the number everytime it's built)

### Step -1 : Preparing script
command to preapre script and making it executable
```
nano  dockersrcipt.sh
```
script used
```bash
#!/bin/bash

# Reading username and  password (making non printable at screen)
read -p "Enter Docker Hub username: " DOCKER_USERNAME
read -s -p "Enter Docker Hub password: " DOCKER_PASSWORD

echo  # Add a new line after password input

# Set Docker Hub credentials as environment variables
export DOCKER_USERNAME
export DOCKER_PASSWORD

#choosing date and time value for unique incremental-versioning
VERSION=$(date +%Y%m%d%H%M)

# Building and tagging the Docker image
docker build -t ${DOCKER_USERNAME}/hello-class:${VERSION} .
docker tag ${DOCKER_USERNAME}/hello-class:${VERSION} ${DOCKER_USERNAME}/hello-class:latest

echo "Docker image built successfully with version: ${VERSION}"
# Push the image to Docker Hub using Docker credential helpers
docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}
docker push ${DOCKER_USERNAME}/hello-class:${VERSION}
docker push ${DOCKER_USERNAME}/hello-class:latest

echo "Docker image pushed successfully to Docker Hub."

# Clean up environment variables so later no one can read my username and password
unset DOCKER_USERNAME
unset DOCKER_PASSWORD
```

### Step -2 : Running script to test
```
chmod +x dockersrcipt.sh
sudo ./dockersrcipt.sh
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q1-T5-1-script-tested.jpg">
</p>

### Step -3 : Veryfying images uploaded or not in Dockerhub
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q1-T5-reviewing-images.jpg">
</p>


# Question 2

## 1. Pull this image `docker pull docker.io/redis:6.0-bookworm`.
```
docker pull docker.io/redis:6.0-bookworm
sudo docker images
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q2-T1-pulling-images.jpg">
</p>

## 2. Find out the commands used to create this image using the command `docker inspect`.

```
sudo docker images
sudo docker inspect redis:6.0-bookworm

# ALso we can inspect direct through registery
docker inspect docker.io/redis:6.0-bookworm
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q2-T2-inspecting-images.jpg">
</p>



## 3. Scan the image and list out all the vulnerabilities that you find.

### Step 1 : installing tools to scan the images
```
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo gpg --dearmor -o /usr/share/keyrings/trivy-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/trivy-archive-keyring.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/trivy.list > /dev/null

sudo apt-get update

sudo apt-get install trivy
```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q2-T3-tool-installation.jpg">
</p>

### Step -2 : Downloading database and Scanning the images

```bash
trivy image --download-db-only
trivy image  docker.io/redis:6.0-bookworm

# we can filter only high and critical vulnerability 
trivy image --severity HIGH,CRITICAL  docker.io/redis:6.0-bookworm
trivy image --severity HIGH,CRITICAL --scanners vuln  docker.io/redis:6.0-bookworm

#We are also able to scan image of local repoitory
trivy image --severity HIGH,CRITICAL  redis:6.0-bookworm
```
Scanning the image
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q2-T3-1-scanning-full.jpg">
</p>

Now , Filtering High and Critical
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q2-T3-1-scanning-high-and-critical.jpg">
</p>

Now , Filtering High and Critical with disabling secret scanning to reduce image scanning time
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q2-T3-3-scanning-filter.jpg">
</p>



## 4. Export this image into `tar` format.
From local images
```
sudo docker save -o /home/subash/docker/redis_6.0-bookworm.tar redis:6.0-bookworm
sudo ls -al /home/subash/docker
```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q2-T4-tar-images-to-local-folder.jpg">
</p>


from registry images
```
# Removing older files
sudo rm /home/subash/docker/redis_6.0-bookworm.tar

sudo docker save -o /home/subash/docker/redis_6.0-bookworm.tar docker.io/redis:6.0-bookworm
sudo ls -al /home/subash/docker
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q2-T4-2-tar-from-registry-images-to-local-folder.jpg">
</p>


# Question 3
## 1. Run the image that you downloaded previously.

```bash
sudo docker images
sudo docker run -it --name redis-subash -d redis:6.0-bookworm
sudo docker ps
```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q3-T1-1-running-images.jpg">
</p>

## 2. Check the CPU and memory usage of the container.
```bash
 sudo docker stats redis-subash
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q3-T2-identifying-resource-usage-container.jpg">
</p>

## 3. Limit the CPU and memory usage to the amount that you see ideal for this container.

```bash
#creating new container after identifying actual resources required to container

# We have create new image from above container to get all our changes
sudo docker run -dit --cpus 0.25 --memory="50m" --name redish-subash2 redis:6.0-bookworm

#updating allocated resources 
sudo docker update --cpus=0.5 --memory=100m redish-subash2
```
Identifying required resources
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q3-T3-1-before-defining-limit.jpg">
</p>

creating new container with manally predefining the resources
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q3-T3-2-defining-limit.jpg">
</p>

Changing the limit of allocated resources
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q3-T3-3-chnaging-limit.jpg">
</p>


# step 4 : We can also use Docker compose

```
Example docker-compose configuration:

version: '3'

services:
  my-service:
    image: my-image
    cpu_period: 50000
    cpu_quota: 25000
    memory: 512m
```

# Question 4

## 1. Write a dockerfile to build the image for the 3-tier application that you developed in earlier assignments.
Pre requirements
```
sudo docker network create bridge-subas --driver bridge
sudo docker inspect bridge-subas

nano Dockerfiledb
nano Dockerfilebackend
nano Dockerfilefrontend
```

scripts of Dockerfile are as follows
### Dockerfiledb
```bash
FROM postgres:latest

# Define arguments for username and password
ARG POSTGRES_USER=myuser
ARG POSTGRES_PASSWORD=mypassword
ARG POSTGRES_DB=mydatabase

# Set environment variables
ENV POSTGRES_USER=$POSTGRES_USER
ENV POSTGRES_PASSWORD=$POSTGRES_PASSWORD
ENV POSTGRES_DB=$POSTGRES_DB

# Expose the PostgreSQL port
EXPOSE 5432
```
you can download script [Dockerfiledb](https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/code/Dockerfiledb)

### Dockerfilebackend
```bash
# Downloading even stable version

FROM node:20-alpine

# Set the working directory
WORKDIR /backend

# Copy package.json and package-lock.json
COPY package*.json /backend/
COPY yarn.lock /backend/


# Install dependencies
RUN yarn install

# Copy the rest of the application
COPY . .

# Expose the backend port
EXPOSE 3000

# Command to run the backend
CMD [ "yarn", "start" ]
```

Download Dockerfile for  backend [Dockerfilebackend](https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/code/Dockerfilebackend)

### Dockerfilefrontend
```bash
# Downloading even stable version

FROM node:20-alpine

# Set the working directory
WORKDIR /frontend

# Copy package.json and package-lock.json
COPY package*.json /frontend/
COPY yarn.lock /frontend/


# Install dependencies
RUN yarn install

# Copy the rest of the application
COPY . .

# Expose the backend port
EXPOSE 5173

# Command to run the backend
CMD [ "yarn", "dev" ]

```
Download Dockerfile for  frontend  [Dockerfilefrontend](https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/code/Dockerfilefrontend)

### Step -2 Building images and creating container
```
# database
sudo docker build -t tododb-image -f Dockerfiledb .
sudo docker run  -itd --name tododb-container -e POSTGRES_USER=subash  -e POSTGRES_PASSWORD=subash@123 -e POSTGRES_DB=tododb --network=bridge-subas -p 5432:5432 tododb-image
```
database image build
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q4-T1-11-db-imagebuild.jpg">
</p>

container creation
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q4-T1-12-db-container-creation.jpg">
</p>


```
#backend
sudo docker build -t todobackend-image -f Dockerfilebackend .
sudo docker run -itd --name todoback-container --network=bridge-subas -p 3000:3000 todobackend-image

```

Backend image build
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q4-T2-21-building-image.jpg">
</p>

container creation
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q4-T2-22-creating-container.jpg">
</p>

```
#frontend
sudo docker build -t todofrontend -f Dockerfilefrontend .

#for specific IP 
sudo docker run -itd --name todofront-container --ip 172.17.0.4 --network=bridge-subas -p 5173:5173 todofrontend
```
Frontend image build
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q4-T2-31-image-building-Front-end.jpg">
</p>

container creation
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q4-T2-32-container-building-Front-end.jpg">
</p>

## 2. Your application should be accessible from the host system.
Testing Database from host
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q4-T1-12-db-connect--table-create.jpg">
</p>

Testing Backed from host
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q4-T2-23-backend-response-testing.jpg">
</p>

Testing Frontend from host
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q4-T2-33-Front-end-testing.jpg">
</p>

manually defining host ip
After Solution
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q4-T1-33-front-end-solution.jpg">
</p>

site is available
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q4-T2-33-frontend-hosting.jpg">
</p>

## 3. Write a bash script to spawn all these containers and make another script to kill those container.

Scrip to spwan
```bash
#!/bin/bash
echo "List of available images"
echo "======================"
sudo docker images
echo " "
echo " "

echo "Before container creation"
echo "======================"
sudo docker ps -a
echo " "
echo " "


 sudo docker network create bridge-subas --driver bridge

# Start container database
echo "==creating database container===="

sudo docker run  -itd --name tododb-container -e POSTGRES_USER=subash  -e POSTGRES_PASSWORD=subash@123 -e POSTGRES_DB=tododb --network=bridge-subas -p 5432:5432 tododb-image

# Start container Backend
echo "==creating backend container===="
sudo docker run -itd --name todoback-container --network=bridge-subas -p 3000:3000 todobackend-image

# Start container front
echo "==creating frontend container===="
sudo docker run -itd --name todofront-container --network=bridge-subas -p 5173:5173 todofrontend

echo " "
echo " "
echo "Containers spawned successfully! and list is"
echo "======================"
sudo docker ps -a
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q4-T3-1-spawn-container.jpg">
</p>

script to kill
```bash
#!/bin/bash
echo "List of container images"
echo "======================"
sudo docker  ps -a
echo " "
echo " "

# Start container database
echo "==Removing database container===="
sudo docker stop tododb-container
sudo docker rm tododb-container

# Start container Backend
echo "==Removing backend container===="
sudo docker stop todoback-container
sudo docker rm todoback-container


# Start container front
echo "==Removing frontend container===="
sudo docker stop todofront-container
sudo docker rm todofront-container

echo " "
echo " "
echo "Containers killed successfully!and list is"
echo "======================"
sudo docker ps -a

```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-mahesh-regmi-subash729/blob/main/materials/Q4-T3-2-kill-container.jpg">
</p>

## 4. Bonus: Make the data persist by using docker volumes in those containers.

#DOing research on how to setup for 3 tier

# Question 5.
1. Convert the **answer** from Q4 into docker-compose equivalent implementation.
2. Running `docker-compose up` should bring all the services up.
3. Bonus: Add healthcheck in your API.
