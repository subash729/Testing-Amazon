# 1. Setup SSH server & only allow SSH port 2202 via firewall

Installing ssh server App and configured as requirement of our question
```
sudo apt update
sudo apt install openssh-server
vi /etc/ssh/sshd_config

sudo systemctl restart ssh
sudo ufw deny 22
sudo ufw allow 2202
sudo ufw enable
```

<B>FIG : SSH server setup and changing port as well as blocking default port via firewall </B>
<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q1-T0-ssh-setup%20and%20firewal.jpg">
</p> 

<B>FIG : Verifying ssh blocked or not </B>

<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q1-T0-ssh-check.jpg">
</p> 

## 1.1 Disable password authentication 
Disabling password authentication check using the configuration file stored at ```"/etc/ssh/sshd_config"```
<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q1_T1-password_disable.jpg">
</p> 

Now testing, password based authentication are blocked or not 
<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q1_T1-disable-auth-check.jpg">
</p> 

<p align="center">
<img src ="">
</p> 

# 1.2 Connect via SSH to that host through key pair 
WE have to use public and private key based authentication for it

step -1 : Creating key pair i.e. public and private key 

<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q1-T2-ssh-key-gen-public-private.jpg">
</p> 

step 2 : Copying public key into the ssh-server and restarting the service
<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q1-T2-ssh-pub-key-adding-to-server.jpg">
</p> 

Step -3 : Checking authentication without password
<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q1-T2-login-without-password.jpg">
</p> 

# 2. Teamwork (individual submission)
## 2.1 Setup passwordless authentication to your team’s SSH server from your server
We create a public and private key and plcae private key into my firend Bijay machine following the similar step of ``` Section 1.2 ``` 


# 2.2 Writing a script to incremental backup from teammate’s server to my local directory hourlySetup passwordless authentication ```
Step -1 : Preparing the script as follows
```bash
#!/bin/bash

set -x

# Defining Friend Details

TEAMMATE_USERNAME="gardener"
TEAMMATE_SERVER="192.168.174.75"


# Rsync command for incremental backup
rsync -avz --delete --progress -e ssh $TEAMMATE_USERNAME@$TEAMMATE_SERVER:/home/gardener/data-bijay /home/subash/data-subash

echo "Backup completed at: $(date)"
```
Testing the script that it will store incrementtal backup or not

### first time run
<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/q2-rsync-copy.jpg">
</p> 

### second time run
<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/q2-rsync-incremental.jpg">
</p> 

Step -2 : Step -2 : Now scheduling to run secript on hourly basis
```
sudo crontab -e
0 * * * *  /home/subash/rsync_copy.sh     

sudo systemctl restart cron
```

<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q2-T2-Cron-and-script.jpg">
</p> 



# 3. Setup MySQL 8.0 server
Download Link : [mysql version 8](https://downloads.mysql.com/archives/community/)
<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q3-T0-%20mysql-download-page.jpg">
</p> 

## 3.1 Create database intern-data
step 1 : Installling and starting database
```
sudo apt install mysql-server
mysql --version
sudo systemctl start mysql
```


Step -2 : Login and Creating Database 
```
sudo mysql -u root -p
CREATE DATABASE intern_data;
SHOW DATABASES;
```
<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q3-T1-start-mysql%20and%20add%20database.jpg">
</p> 

sudo systemctl start mysql
## 3.2 Create user that can authenticate with local linux user account instead of a separate mysql user account

```
sudo mysql -u root -p

CREATE USER 'subash'@'localhost' IDENTIFIED WITH auth_socket;
GRANT ALL PRIVILEGES ON intern_data.* TO 'subash'@'localhost';
FLUSH PRIVILEGES;
```
User creation

<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q3-T2-assign-local-user-privillage-on-database.jpg">
</p> 

checking usser can login or not

<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q3-T2-checking-local-user-login.jpg">
</p> 

## 3.3 Create another local user that can authenticate through another host remotely but allow only a single host/IP address only
Step -1 : Creating User for remote login and Assigning the Privillege 
```
CREATE USER 'remote_subash'@'192.168.253.131' IDENTIFIED BY 'pwdleapfrog';
GRANT ALL PRIVILEGES ON intern_data.* TO 'remote_subash'@'192.168.253.131';
FLUSH PRIVILEGES;
```

<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q3-T3-Remote-User-Creation.jpg">
</p> 

Step :2 Allowing allow remote connection because by default local host is binded only 
As well as allwign in firewall. We will change bind address of file located at ```/etc/mysql/mysql.conf.d/mysqld.cnf```
```
ufw allow 3306
ufw enable
```

<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q3-T3-Remote-User-Creation.jpg">
</p> 

step -3 Checking
mysql -u remote_subash -p -h 192.168.253.130




<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q3-T3-Login-Remote-User-test.jpg">
</p> 


## 3.4 Provide read only access to DB intern-data to that local user
Step -1 : Granting Readonly to local system user
```
mysql -u root -p
GRANT SELECT ON intern_data.* TO 'subash'@'localhost';
FLUSH PRIVILEGES;
```
<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q3-T4-Assign-Readonly-User-privillege.jpg">
</p> 



Step -2 : Testing
```
USE intern_data;
mysql -u subash -p
desc employees;
```
<p align="center">
<img src ="https://github.com/LF-DevOps-Training/feb-14-linux-system-prajwol-subash729/blob/main/materials/Q3-T4-Test-Read-only-privillege.jpg">
</p> 



## 3.5 Setup another MySQL server and create realtime master-master replication

<p align="center">
<img src ="">
</p> 








