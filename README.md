# 1. Restricted Bash

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


### Login and then check for allowed and disallowed commands
```
su - subash_que2
sudo ls
sudo mkdir
```
Running commands
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/Q2-T1-3-USer%20only%20allow%20ls%20and%20cat.jpg">
</p>


# 3. Write bash script that includes:
Upon execution, log string: “Test Complete” using rsyslog config (use filtering other than messages) with logger command to a custom file

Execute script with its filename from any directory in terminal without specifying the full path & interpreter of the script

systemctl restart rsyslog


```
#!/bin/bash

# Log string to custom file using rsyslog and logger command
logger -p user.0 "Test Complete"

```

# 4 Linux LVM

## 4.1 Attaching 2 storage Disk
Disk status before adding the DIsk
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/3.Before-ssh.jpg">
</p>
Now adding the Disk
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/4.1%20Adding%20Disk.jpg">
</p>

final step of adding 
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/4.2%20Adding%20Disk%20final.jpg">
</p>

Reviewing the Disk 
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/4.3%20Adding%20Review.jpg">
</p>

Login into host OS for checking 
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/4.4%20Disk%20checking%20is%20OS.jpg">
</p>




## 4.2 Creating PV and VG
STEP -1 : Formating the above mounted disk so that they support LVM

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

<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/4.5-Q2-Disk-format-with-LVM.jpg">
</p>


<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/4.6-Q2-Disk-format-with-LVM-step-2.jpg">
</p>

Step - 2 : Installing tools, and creating LVM

```
apt install lvm2 -y
fdisk -l | grep LVM
pvcreate /dev/sdb1 /dev/sdc1
pvs

vgcreate RAID_FOR_LOG /dev/sdb1 /dev/sdc1
vgs

```
Installing tools to create PV
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/4.7-Q2-PV-Creation-2.jpg">
</p>

Volume group creation
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/4.8-Q2-VG-Creation-2.jpg">
</p>

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
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/4.9-Q2-LV-Creation-2.jpg">
</p>


### step -2 filesystem assign
```
mkfs.ext4 /dev/mapper/RAID_FOR_LOG-RAID_1_V_DISK
mkfs.ext4 /dev/mapper/RAID_FOR_LOG-RAID_1_V_DISK2
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/4.10-Q2-assigning-file-system-2.jpg">
</p>



### step - 3 Mounting file system 1
```bash
# /etc/fstab: static file system information.
#
/dev/mapper/RAID_FOR_LOG-RAID_1_V_DISK /mnt/LVM1 ext4 defaults 0 0
/dev/mapper/RAID_FOR_LOG-RAID_1_V_DISK2 /mnt/LVM2 ext4 defaults 0 0
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/4.10-Q2-assigning-file-system-2.jpg">
</p>


### Mounting and checking FIles-system
```
mount -a
df -hT
```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/4.11-Q2-mounting.jpg">
</p>

## Task -2 
Now Formatting LV for RAID so we can choose partion as RAID
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/Q4-T2-using-LV-for-RAID.jpg">
</p
  
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/Q4-T2-using-LV-for-RAID-1.jpg">
</p>

Now, rebootign the system todapt changes. Partition is changed as RAID
<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/Q4-T2-using-LV-for-RAID-detect.jpg">
</p>

configuring the RAID level 1


<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/Q4-T2-using-LV-for-RAID-configure.jpg">
</p>


### 4.4 Now, mounting into /var
fomating with ext4 and mounting at mentioned point and done in /etc/fstab so it will mount dueing boot time

<p align="center">
<img src="https://github.com/LF-DevOps-Training/getting-started-with-linux-system-subash729/blob/main/materials/Q4-T2-Format-file-system-and-mount-fstab.jpg">
</p>

