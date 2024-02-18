## TASK 2 : connecting client via GUI and CLI After generating config for client

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


