# Q1. Create an OpenVPN server & connect client via GUI and CLI

## Task -1 Openvpn Server setup
### Step 1: Updating and Upgrading Ubuntu
We are updating and upgrading our Ubuntu system before installing the openvpn.

```
sudo apt update 
sudo apt upgrade
```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-0-update-and-upgrade.jpg">
</p>


### Step -2 Installing Required Package  (OpenVPN and easy-rsa)
```
sudo apt install openvpn easy-rsa
```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-1-Openssh-easy-rsa-installation.jpg">
</p>

### Step -3 Generate Certificates and Keys
```
make-cadir ~/openvpn-ca && cd ~/openvpn-ca
nano ./vars
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ2-T1-2-CA-Direcroty-create-Var-setup.jpg">
</p>

```
set_var EASYRSA_REQ_COUNTRY    "NP"
set_var EASYRSA_REQ_PROVINCE   "Bagmati"
set_var EASYRSA_REQ_CITY       "Kathmandu"
set_var EASYRSA_REQ_ORG        "Leapfrog"
set_var EASYRSA_REQ_EMAIL      "subash@example.net"
set_var EASYRSA_REQ_OU         "IT"
```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-2.1-Var-setup.jpg">
</p>


### Step 3.1 Generating Required ceretificates and files
```
./easyrsa init-pki
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ2-T1-3-PKI-Initialization.jpg">
</p>

```
./easyrsa build-ca
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-4-build-ca.jpg">
</p>

```
./easyrsa gen-req server nopass
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-5-gen-req.jpg">
</p>


```
./easyrsa sign-req server server
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-6-sign-req.jpg">
</p>

```
./easyrsa gen-dh
```
Started to generate Diffieman-Hash
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-7-gen-dh.jpg">
</p>

Diffieman-Hash generation completed
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-7.1-gen-dh-complete.jpg">
</p>


```
openvpn --genkey --secret pki/ta.key
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-8-pki-ta.jpg">
</p>


### Step 4: Configure OpenVPN
Copying server config
```
sudo cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf  /etc/openvpn/server.conf
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-9-copying%20server-config-file.jpg">
</p>


copying certificates and key
```
sudo cp /home/subash/openvpn-ca/pki/{ca.crt,dh.pem,ta.key} /etc/openvpn
sudo cp /home/subash/openvpn-ca/pki/issued/server.crt /etc/openvpn
sudo cp /home/subash/openvpn-ca/pki/private/server.key /etc/openvpn
```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-10-ccopying%20required-certificates.jpg">
</p>


Now configuring server config of  (```/etc/openvpn/server.conf```)
```
ca ca.crt
cert server.crt
key server.key  
dh dh.pem

;tls-auth ta.key 0
tls-crypt ta.key
```
Changing config 
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-11-editing-server-config.jpg">
</p>

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-11.1-editing-server-config.jpg">
</p>



enable Ip forwarding
```
sudo nano /etc/sysctl.conf
# Uncomment the following line:
net.ipv4.ip_forward=1
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-12-sysctl-config.jpg">
</p>


Appplying the changes
```
sudo sysctl -p
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-12.1-applying-sysctl-config-change.jpg">
</p>

### Step -5 Starting and Enable OpenVPN

```
sudo systemctl start openvpn@server
# Restart the service if it is started already
sudo systemctl restart openvpn@server
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-13-Restarting-openvpn-service.jpg">
</p>

Enabling the service so that it can start automatic after host turned on
```
sudo systemctl enable openvpn@server
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T1-14-enabling-the-service.jpg">
</p>

## Task -2 Client login and connnect to Openvpn Server

