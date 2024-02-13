#1 Restricted Bash

## 1.1 Create a user with its home directory & restricted bash shell

```
sudo useradd -m -d /home/subash_test -s /bin/rbash subash_test
sudo passwd subash_test
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/Q1-create-user.jpg">
</p>

## 1.2 Block “pwd” command as well
Just using alias to change pwd value so no original pwd command will run
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/Q1-defne-alias.jpg">
</p>

# 2. User/Group Permissions

```
### Creating user and group 
useradd -m -d /home/subash_que2 -s /bin/bash subash_que2
passwd subash_que2
addgroup restrictedgroup
usermod -aG restrictedgroup subash_que2
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/Q2-T1-1-creating-user-and-group.jpg">
</p>


sudo visudo

### Allowing Few commands
%restrictedgroup ALL=(ALL) /bin/cat, /bin/ls

<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/Q2-T1-2-visudo-edit.jpg">
</p>


###Login and then check for allowed and disallowed commands
```
su - subash_que2
sudo ls
sudo mkdir
```
Running commands
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/Q2-T1-3-USer%20only%20allow%20ls%20and%20cat.jpg">
</p>


<p align="center">
<img src="">
</p>



#3.

systemctl restart rsyslog


```
#!/bin/bash
#!/bin/bash

# Sending Log string to rsyslog
log_dir="/var/log/rsyslog"
mkdir -p $log_dir

logger -p systemerror "Test Complete"



```

#4 LInux LVM


## 4.2 Creating PV and VG

```
fdisk /dev/sdb

Command (m for help): n

Select (default p): p

Partition number (1-4, default 1):

First sector (2048-20971519, default 2048):

Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-20971519, default 20971519):

Command (m for help): t

Selected partition 1

Hex code or alias (type L to list all): L

Hex code or alias (type L to list all): 8e


Command (m for help): p


Command (m for help): w
```

step - 2 : Installing tools, and creating LVM

```
apt install lvm2 -y
fdisk -l | grep LVM
pvcreate /dev/sdb1 /dev/sdc1
pvs

vgcreate RAID_FOR_LOG /dev/sdb1 /dev/sdc1
vgs

```

## 4.3 Creating LV to configure RAID

### Task-1
```
### step-1 LV creation
vgs
lvcreate -L 8G -n RAID_1_V_DISK
vgs
lvcreate -L 8G -n RAID_1_V_DISK RAID_FOR_LOG
lvcreate -L 8G -n RAID_1_V_DISK2 RAID_FOR_LOG
lvs

### step -2 filesystem assign
mkfs.ext4 /dev/mapper/RAID_FOR_LOG-RAID_1_V_DISK
mkfs.ext4 /dev/mapper/RAID_FOR_LOG-RAID_1_V_DISK2
```



### step - 3 ounting file system 1
```bash
# /etc/fstab: static file system information.
#
/dev/mapper/RAID_FOR_LOG-RAID_1_V_DISK /mnt/LVM1 ext4 defaults 0 0
/dev/mapper/RAID_FOR_LOG-RAID_1_V_DISK2 /mnt/LVM2 ext4 defaults 0 0
```
### Mounting and checking FIles-system
```
mount -a
df -hT
``

## Task -2 


