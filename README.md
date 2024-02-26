# A. *Configs and Secrets*

## Question 1. Install the latest version of docker and compose.

### Step -1 : Adding repository with security and updating the package repository

Reading new updated package and and Docker  GPG key to verify  authenticity and integrity of the software package. We also add repository of docker and update so, we get the updated package while installing new package.

```bash
sudo apt update
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null


sudo apt-get update
```
Updating repository and reading new package list
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-services-and-swarm-pranav-pudasaini-subash729/blob/main/materials/QA-T1-11-adding%20and%20updating-repo.jpg">
</p>


### step -2 
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo docker --version
sudo docker compose version
```
Updated Version
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-services-and-swarm-pranav-pudasaini-subash729/blob/main/materials/QA-T1-12-installing%20and%20checking%20-version.jpg">
</p>

## Question 2. Explore Compose docs to understand the supported types of configs and secrets.

Architecture
- **Version and name top-level elements**
  - The "version" property in the Compose Specification is primarily for backward compatibility and is informative rather than prescriptive. Compose does not use the version to enforce schema validation but instead favors the most recent schema 
  
- Services top-level elements  
- Networks top-level elements
- Volumes top-level elements
- Configs top-level elements
- Secrets top-level elements

### 1. **Configs top-level elements**
   
- **1.1 Environment Variables:**
   We can set environment variables for services in the ```docker-compose.yml``` file. These variables are used to configure various aspects of the containerized applications. For e.g.
   
   ```yml
   services:
    web:
        image: nginx
        environment:
            - MY_VARIABLE=anyvalue
   ```
- 1.2  **Configuration Files:**
We can also use configuration files in services to provide additional configuration details. For e.g.
```yml
services:
  web:
    image: nginx
    configs:
        http_config:
            file: /etc/nginx/conf.d/default.conf
```
Example -2
```<project_name>_app_config``` is created when the application is deployed, by registering the inlined content as the configuration data. This means Compose infers variables when creating the config, which allows you to adjust content according to service configuration:

```yml
configs:
  app_config:
    content: |
      debug=${DEBUG}
      spring.application.admin.enabled=${DEBUG}
      spring.application.name=${COMPOSE_PROJECT_NAME} 
```

[Official Reference link](https://docs.docker.com/compose/compose-file/)

### 2. Secrets top-level elements
Docker Compose supports managing secrets, which are sensitive pieces of data such as API keys, passwords, etc. Secrets are stored securely and made available to the containers. They are usually used in conjunction with Docker Swarm.

For example  1
```yml
services:
  db:
    image: postgres
    secrets:
      - db_password
secrets:
  db_password:
    file: ./secrets/db_password.txt
```
For example -2 for token

```yml

services:
  db:
    image: postgres
    secrets:
      - db_password

secrets:
  db_password:
    file: ./secrets/db_password.txt
  token:
    external: true
```


## Question 3. Use secrets and configs as applicable in your dockerized 3-tier application.

## Question 4. Make sure that you are running docker commands without sudo and the user inside the container is also not root.

### Step -1  Adding user to docker group 
```
#For adding current login user 
sudo usermod -aG docker $USER

#For adding other user 
sudo usermod -aG docker testuser

logout
ssh username@<host-ip>
docker ps
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-services-and-swarm-pranav-pudasaini-subash729/blob/main/materials/QA-T4-allowedocker-non-root.jpg">
</p>

## Step -2 Creating image to run container with non root user
We download a Linux image, then create a user without adding it to the root group during image building. We log in with the specified user and shell that are defined for the user.

```
nano Dockerfile
docker build -t non-root-image .
docker run -it --name non-root-container --user subash non-root-image /bin/sh

#Run any command
```

Used Dockerfile
```Dockerfile
# Use a base image with your desired OS
FROM ubuntu:latest

# Create a non-root user and switch to it
RUN useradd -m -s /bin/sh subash
USER subash

# Set the working directory
WORKDIR /user1

CMD echo you are login as non root user
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-23-docker-services-and-swarm-pranav-pudasaini-subash729/blob/main/materials/QA-T4-2-container-with-non-root.jpg">
</p>


# B. *Debugging*

##  Question 1. 
## You have been tasked to debug this Docker image docker.io/ppbro/ggmeter:v1. For some strange reason, port 8080 is inaccessible from the host. You need to fix this image, use `docker commit` to create a new image, and publish it to DockerHub.

### Step - 1 : Building container and checking connectivity 

In this stage, we create a container and verfying connectivity  and port binding of host with container. We also verify some logs 
```bash
docker run -d -p 8080:8080 --name ggmeter-debug docker.io/ppbro/ggmeter:v1
docker ps -a
curl 192.168.140.138:8080
docker logs ggmeter-debug

netstat -tulpn | grep 8080
docker exec -it ggmeter-debug netstat -tulpn | grep 8080
docker container inspect ggmeter-debug
```

<p align="center">
<img src="">
</p>
Everything is seems to be fine in host while container execute a serve.sh script and also we find port is exposed but bind with container loopback ip.

<

### Step -2 Finding issue on container
We will check serve.sh config because conntainer shows it execute script serve.sh
```bash
docker exec -it ggmeter-debug /bin/sh
ls -al
vi serve.sh
exit
docker stop ggmeter-debug
docker start ggmeter-debug

```

```bash
#!/bin/sh

python3 -m http.server -b 0.0.0.0 8080
```
<p align="center">
<img src="">
</p>



### Step -3 Restarting Container and testing connectivity
```bash
curl localhost:8080
curl 192.168.140.138:8080
```
<p align="center">
<img src="">
</p>

### Step -4 Creating new image and puhing it to dockerhub
```bash
docker stop ggmeter-debug
docker commit ggmeter-debug subash-ggmeter
docker login
docker tag subash-ggmeter subash729/subash-ggmeter
docker push subash729/subash-ggmeter
```
<p align="center">
<img src="">
</p>


# C. *Swarm*

## Question 1. 
## Go to https://labs.play-with-docker.com, create a swarm with 1 master and 2 worker nodes.
Initializing swarm
```bash
#TO initialize as master
docker swarm init --advertise-addr eth0

# To join as worker
docker swarm join --token SWMTKN-1-1fdxh2b3r78cbhizpuvo8uoytfkdenio87kau8ikawqppxid46-b57ixhzghph95a51drk81ofne 192.168.0.8:2377
```
Master initialize
<p align="center">
<img src="">
</p>

Slave initialize
<p align="center">
<img src="">
</p>


## Question 2. 
## Write Docker Stack YAML to run 5 replicas of the busybox container. Provide screenshots showing the containers running on both worker nodes and the master node.
```bash
docker stack deploy -c docker-compose.yml subashstack
docker ps -a
```

yaml comfig of docker-compose is
```yml
version: "3"

services:
  busybox:
    image: busybox
    deploy:
      replicas: 5
```
Master nodes container
<p align="center">
<img src="">
</p>

slave node container
<p align="center">
<img src="">
</p>

2nd slave node container
<p align="center">
<img src="">
</p>

## Question 3. 
## Why is the master node running containers? Change your swarm configuration so that only the worker nodes run the services.

Logic : composing docker as no any container should be run on node who is working as master.
```bash
docker stack deploy -c docker-compose.yml subashstack
docker ps -a
```
```bash
version: "3"
services:
  busybox:
    image: busybox
    deploy:
      placement: 
        constraints: 
          - node.role != manager
      replicas: 5
```

Master nodes with no any container
<p align="center">
<img src="">
</p>

slave node with  container
<p align="center">
<img src="">
</p>

4. Deploy your 3-tier application as a Docker Stack in a Swarm. The containers should be distributed across 1 master and 2 worker nodes. Hint: You might need to make your compose file compatible with Stack.

Step 1 : Pushing 3-tier images to dockerhub and pulling to dockerplay
```bash
#local to docker hub
docker push subash729/tododb-image
docker push subash729/todobackend-image
docker push subash729/todofrontend-image

# dockerhub to docker play
docker pull subash729/tododb-image
docker pull subash729/todobackend-image
docker pull subash729/todofrontend-image
```
<p align="center">
<img src="">
</p>

5. Scale-up your backend replicas to 20. Did you face any issue with the stack?
