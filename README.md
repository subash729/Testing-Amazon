## 1. What is containerization ? How does it differs from virtualization ? 

<div align="justify"> 
Containerization is a technology that packages and isolates software applications along with their dependencies to  consistently across different systems. It simplifies deployment  of application with enhancing scalability, efficiency as well as  resolving package dependency problem while migrating application in production enviorment from one system to another. 
</div>

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-22-containerd-binayasharma-subash729/blob/main/materials/Q1-Architecture-2.jpg">
</p>


- **Resource Utilization:** 
<div align="justify"> 
  Containers share the host OS kernel, leading to more efficient resource utilization, while virtualization involves running multiple complete OS instances, each with its kernel, which can be resource-intensive.

  
</div>


- **Portability**
<div align="justify"> 

  Containers are more portable, as they encapsulate both the application and its dependencies, making it easier to move between environments. Virtual machines are bulkier and less portable due to their larger size and reliance on hypervisors.
</div>


- **Isolation**:
<div align="justify">
 Containers provide process-level isolation, ensuring applications run independently, but share the same OS kernel. Virtualization offers stronger isolation, as each virtual machine operates with its own complete OS, leading to higher security but potentially higher overhead. 
</div> 

- **Overhead:** 
 <div align="justify"> 
 Containers have lower overhead, as they don't require a full OS per instance, resulting in faster startup times and less resource consumption. Virtual machines, with complete OS copies, have higher overhead due to the need for separate kernels.
</div>

- **Performance:** 
<div align="justify"> 
Containers generally offer better performance due to their lightweight nature and shared kernel. Virtual machines may have more overhead and slower performance because of the need for individual OS instances.
</div>


- **Use Case:**
<div align="justify"> 
</div> Containers are suitable for microservices architectures and cloud-native applications, emphasizing agility and scalability. Virtualization is often preferred for running multiple diverse workloads on a single physical server, providing strong isolation but with higher resource overhead.

- **Cost of implementation**
<div align="justify"> 
Containerization is generally more cost-effective than virtualization as it utilizes resources more efficiently, allowing for higher application density on a host system, reducing infrastructure costs. Virtualization may incur higher costs due to the overhead of running multiple complete operating systems on the same hardware.
</div>



## 2. What is the role of containerization in microservice architecture ? 

<div align="justify">
Microservice architecture is a system/application design approach where an system is developed as a set of small, independent services, each serving a specific function. These services can be developed and deployed separately, promoting flexibility and scalability. This approach improves stability and allows for easier maintenance and updating in complex system and software.
</div>


1. **Isolation and Independence:**
- Containers encapsulate individual microservices and their dependencies, ensuring isolation. Each microservice runs within its own container, preventing interference with other microservices.

- Independence allows microservices to be developed, tested, and deployed independently, promoting agility and flexibility.


2. **Resource Efficiency:**

- Containers share the host OS kernel, making them lightweight compared to traditional virtual machines. This leads to efficient resource utilization, allowing more microservices to run on the same infrastructure.
Rapid startup times and low overhead contribute to faster deployment and scaling.


3. **Scalability:**

- Containers provide a scalable environment for microservices. They can be quickly scaled up or down based on demand without the need for lengthy provisioning times.

- Container orchestration tools, such as Kubernetes or Docker Swarm, automate the management of containerized microservices, ensuring they are distributed efficiently across a cluster of machines.


4. **Portability:** 
- Containers package an application along with its dependencies and runtime into a single unitto makes easier for moving microservices across different environments, from development to testing to production by reducing deployment issues.


5. **Fault Isolation:**

- Containerization helps in containing faults to specific microservices. If a containerized microservice fails, it doesn't affect the entire application, ensuring resilience and fault tolerance.

6. **Versioning and rollback:**

- Containers support versioning, so multiple versions of microservices are possible. This allows for a more smooth deployment and easier rollback if there are problems with a new version.


## 3. Follow the [link](https://bs14.github.io/cgroup-demo/) and simulate the memory limits. Troubleshoot if there is any issue. Provide screenshots. 

### Step -1 : Creating a new cgroup and Adjusting values to limit the memory

```bash
mkdir -p /sys/fs/cgroup/demo
echo "50000000" > /sys/fs/cgroup/demo/memory.max
echo "0" > /sys/fs/cgroup/demo/memory.swap.max
ls -al /sys/fs/cgroup/demo | grep "memory.*.max"
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-22-containerd-binayasharma-subash729/blob/main/materials/Q3-1-cg-group-file-creation.jpg">
</p>


### Step - 2 : Assign current PID to the cgroup
```bash
cat /sys/fs/cgroup/demo/cgroup.procs
echo $$ > /sys/fs/cgroup/demo/cgroup.procs
cat /sys/fs/cgroup/demo/cgroup.procs
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-22-containerd-binayasharma-subash729/blob/main/materials/Q3-2-cg-group-pid-storing.jpg">
</p>


### Step -3 Script used to consume resources

```python
import time

# Open /dev/urandom as a binary file
f = open("/dev/urandom", "rb")

# Initialize an empty byte string
data = b""

# Counter for iteration
i = 0

# Infinite loop to read 10MB chunks from /dev/urandom
while True:
    # Read a 10MB chunk from /dev/urandom
    chunk = f.read(10000000)  # 10MB

    # If no more data can be read, break out of loop
    if not chunk:
        break
    
    # Concatenate the chunk to the data variable
    data += chunk
    i += 1

    # Print cumulative size in MB
    print(f"{i * 10}MB")
    time.sleep(5)

```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-22-containerd-binayasharma-subash729/blob/main/materials/Q3-3-script-tested.jpg">
</p>


### Step -4 : Verifying OOM kill
```
grep oom /var/log/kern.log
```
Veryfying the log
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-22-containerd-binayasharma-subash729/blob/main/materials/Q4-4-kernal-log.jpg">
</p>

## 4. How do we prevent fork bomb using concept of cgroup ? 
<div align="justify"> 
A fork bomb is a type of denial-of-service (DoS) attack on a computer system. It is a malicious software or script designed to create a large number of processes rapidly, consuming system resources and causing the system to become unresponsive or crash. The term "fork" refers to the system call used to create a new process in Unix-like operating systems.
</div> 
Example
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-22-containerd-binayasharma-subash729/blob/main/materials/Q4%20-%20fork-bomb-example.jpg">
</p>

Working mechanism of Fork bomb
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-22-containerd-binayasharma-subash729/blob/main/materials/Q4%20-%20fork-bomb-process.jpg">
</p>


- Resource Control with Cgroups:
<div align="justify"> 
Cgroups allow you to allocate and limit system resources, such as CPU, memory, and processes, to specific groups of processes.
</div>  

- Limiting Process Creation:


<div align="justify"> 
Use cgroups to set limits on the maximum number of processes that a specific cgroup (group of processes) can create. This restricts the fork bomb's ability to spawn an unlimited number of processes.
</div> 

- Create a Cgroup for Fork Bomb Prevention:
<div align="justify"> 
Establish a cgroup specifically designed to prevent fork bombs. This involves creating a new cgroup and configuring resource limits for that cgroup.
</div> 


- Set Process Limits in the Cgroup:
<div align="justify"> 
Within the designated cgroup, set the maximum number of processes allowed. This can be achieved by adjusting the appropriate cgroup parameters to restrict the number of forks that can be performed.
</div> 


- Monitoring and Enforcement:
<div align="justify"> 
Regularly monitor the resource usage of the cgroup to detect any unusual or excessive behavior. Cgroups provide a mechanism to enforce resource limits, preventing processes from exceeding the defined constraints.
</div> 


- Isolation and Security:
<div align="justify"> 
Utilize cgroups to isolate the fork bomb within its designated cgroup, preventing its impact from affecting other parts of the system. This isolation enhances system security and prevents the fork bomb from disrupting the entire system.
</div> 

## 5. Run a process and limit the CPU usage to only 5% using cgroup. Provide the code and screenshots to show process gets killed.

```bash
sudo apt install cgroup-tools
sudo apt install python3-pip
pip install psutil

```
code
```python
import psutil
import time

def get_cpu_usage():
    return psutil.cpu_percent(interval=1)

def main():
    try:
        while True:
            cpu_usage = get_cpu_usage()
            print(f"CPU Usage: {cpu_usage}%")
            time.sleep(1)
    except KeyboardInterrupt:
        print("\nExiting the program.")

if __name__ == "__main__":
    main()
```
## 6. Create upper and lower directories, and place the files in it. Use an overlay mount to simulate the Union File System. Provide the screenshot of the merged filesystem, and the commands used. 

#### Step -1 : Creating directory and files to test overlay

We create three directories: 'upper,' 'lower,' and 'merged' as working directory. We create two files as same name in both 'upper' and 'lower' directory and another file with different name.

```bash
 mkdir lower upper merged
 echo "print (Hello overlay upper)" > upper/code.py
 echo "Content of anoter file" > upper/script.py
 echo "print (Hello overlay lower)" > lower/code.py
 echo "Config of second file of lower" > lower/script.py

```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-22-containerd-binayasharma-subash729/blob/main/materials/Q6-before-merge-files-status.jpg">
</p>

Now let's test overlay with filesystem type overlay, working directory as merged and '/mnt/merged' as mounting directory after overlay
```bash
sudo mkdir /mnt/merged
sudo mount -t overlay overlay -o lowerdir=./lower,upperdir=./upper,workdir=./merged /mnt/merged

ls -al  merged/
ls -al  /mnt/merged/
cat /mnt/merged/code.py
cat /mnt/merged/script.py
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-22-containerd-binayasharma-subash729/blob/main/materials/Q6-after-merged-files-status.jpg">
</p>

## 7. Write in brief about the Docker architecture.
<div align="justify"> 
Docker is an open source centralized platform designed to create, deploy and run applications to eliminate 
compatibility of packages and library dependency while migrating code from one system to another. Docker 
uses container on host os to run application. It allows applications to use same Linux kernel as a system on 
the host computer, rather than creating a whole virtual OS.
<div>

Architecture of docker is given as follows:
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-22-containerd-binayasharma-subash729/blob/main/materials/Q7-Docker-Architecture.jpg">
</p>

Major components of docker are gives as follows
- Docker Client
- Docker host
- Docker Daemon
- Docker Image
- Docker Container
- Docker hub / registry

1. **Docker Daemon**
- The Docker daemon, or dockerd, is a background process that manages docker container operations. 

- It takes care of tasks such as creating, running, stopping, deploying containers and communicates with the docker CLI and manages the execution of containers on the host system. 

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-22-containerd-binayasharma-subash729/blob/main/materials/Q7-1-Docker-demon.jpg">
</p>


2. **Docker Client**
- The Docker client is a command-line tool that allows users to interact with the Docker daemon. 

- It serves as the interface between the user and the Docker daemon, enabling actions like building, running, and stopping containers.

- Users issue commands through the Docker CLI (Command Line Interface) to manage and control Docker containers. 

- It uses REST API to communicate with docker daemon.


3. **Docker host**
- The Docker host is the physical or virtual machine where Docker is installed and runs. 

- It serves as the environment for creating, managing, and executing Docker containers and responsible for running the Docker daemon, managing resources, and hosting containerized applications. 

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-22-containerd-binayasharma-subash729/blob/main/materials/Q7-2-Docker-host.jpg">
</p>


4. **Docker image**
- Docker Images are made up of multiple layers of read-only filesystems, these filesystems are called a Docker file, they are just text file with a set of pre-written commands. 
  
- A Docker image is a lightweight, standalone, and executable package that includes everything needed to run a software application, including the code, runtime, libraries, and system tools. 

- It is a snapshot of a file 
system and configuration settings. Images are used to create and run Docker containers consistently across different environments, ensuring portability and reproducibility.


5. **Docker Container**

- A container is a lightweight, runnable instance of a Docker image.

-  It is an isolated application built from one or more images which include  all the libraries and dependencies required for an application to run.

- Containers are portable, scalable, and can 
run consistently across various environments, facilitating easy deployment and management of 
applications. 

- Container offer a standardized and efficient way to package, distribute, and run software.
  
- Containers share the host's operating system kernel while remaining isolated from each other.

6. **Docker registry / Docker hub**
- It is the place where Docker images are stored.
- Docker Hub is a cloud-based registry for sharing and accessing Docker container images. 

- users / developer  can pull and push images to and from Docker Hub making easy distrubution and deplyement  process with increasing collabration.

- It has two registry
  - Public registry / docker hub
  - Private registry / docker enterprise
