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


## TASK 2 : Connecting client via GUI and CLI After generating config for client

### Step 1 : Generating required certificate and key

```
./easyrsa gen-req client1 nopass
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-15-client-gen-req.jpg">
</p>


```
./easyrsa sign-req client client1
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-16-client-sign-req.jpg">
</p>


We have to enter old server pass key which we had done setup at previous step 

### Step -2 : copying client configuration, cetificate and key to system cofiguration
```
sudo cp pki/private/client1.key /etc/openvpn/client/
sudo cp pki/issued/client1.crt /etc/openvpn/client/
sudo cp pki/{ca.crt,ta.key} /etc/openvpn/client/
```
Copying required client configuitation to server so that client can authenticate

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-17-copying-client-configuration-to-etc.jpg">
</p>


Old config
```
remote my-server-1 1194
;user nobody
;group nogroup
```

New config
```
remote 192.168.140.135 1194 
user nobody
group nogroup
```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-18.1-client-config-change-1.jpg">
</p>


Old config
```
ca ca.crt
cert client.crt
key client.key
tls-auth ta.key 1
```

New config
```
;ca ca.crt
;cert client.crt
;key client.key
;tls-auth ta.key 1
key-direction 1
```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-18.2-client-config-change-2.jpg">
</p>

Final config
```
remote 192.168.140.135 1194 
user nobody
group nogroup
;ca ca.crt
;cert client.crt
;key client.key
;tls-auth ta.key 1
key-direction 1
```

### Step 3 : Creating Script to generate client configuration of .ovpn

```
touch gen_config.sh
nano gen_config.sh
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-19-Creating-script-for-client-config.jpg">
</p>

```bash
#!/bin/bash
# First argument: Client identifier
KEY_DIR=/etc/openvpn/client
OUTPUT_DIR=/root
BASE_CONFIG=/root/openvpn-ca/client.conf
cat ${BASE_CONFIG} \
    <(echo -e '<ca>') \
    ${KEY_DIR}/ca.crt \
    <(echo -e '</ca>\n<cert>') \
    ${KEY_DIR}/${1}.crt \
    <(echo -e '</cert>\n<key>') \
    ${KEY_DIR}/${1}.key \
    <(echo -e '</key>\n<tls-crypt>') \
    ${KEY_DIR}/ta.key \
    <(echo -e '</tls-crypt>') \
    > ${OUTPUT_DIR}/${1}.ovpn
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-19.1-script-for-client-config.jpg">
</p>

### Step -4 : Generating config and sending to client
```
chmod 700 /root/openvpn-ca/config_gen.sh
./gen_config.sh client1
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-19.2-Testing-script.jpg">
</p>


### Step -5 Copying cofing to client end

```
scp client1.ovpn subash@192.168.140.133:/tmp
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-20-copying-scipt-to-client.jpg">
</p>

### Step 6 : Testing from Client end
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-20-Testing-connectivity.jpg">
</p>

Still trouble shooting issue: 

Old issue

Error1
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/error%20-1.jpg">
</p>
Error2

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/error%202.jpg">
</p>

Error3

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/error-3.jpg">
</p>

Soltions on server end
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/solution-1.jpg">
</p>

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/soltion2.jpg">
</p>

solution on Both client and server end
changed cipher, new client file generated and tested
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/solution%203.jpg">
</p>

# Monday Troubleshoot
## Copying certificate to client device


<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-21-copying-certificates.jpg">
</p>

### copying it to ```/etc/openvpn/client ``` directory

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-21.1-copying-certificates-tio-client-openvpn.jpg">
</p>


Manually Defining client openvpn-config
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-22-client1-config.jpg">
</p>

# Testing

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-22.1-client1-config-testing.jpg">
</p>
<p align="center">
<img src="">
</p>

## Troubleshooting at server side
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-23.1-server-config-change.jpg">
</p>


<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-23.2-server-config-change.jpg">
</p>

## Testing
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-16-system-network-assignment-subash729/blob/main/materials/QQ1-T2-23.3-Testing.jpg">
</p>

Still doing research on it


