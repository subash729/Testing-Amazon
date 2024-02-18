Step 1: Updating and Upgrading Ubuntu
We are updating and upgrading our Ubuntu system before installing the openvpn.

```
sudo apt update 
sudo apt upgrade
```

Step -2 Installing OpenVPN
```
sudo apt install openvpn easy-rsa
```

Step -3 Generate Certificates and Keys
$ make-cadir ~/openvpn-ca && cd ~/openvpn-ca
$ nano ./vars

```
set_var EASYRSA_REQ_COUNTRY    "NP"
set_var EASYRSA_REQ_PROVINCE   "Bagmati"
set_var EASYRSA_REQ_CITY       "Kathmandu"
set_var EASYRSA_REQ_ORG        "Leapfrog"
set_var EASYRSA_REQ_EMAIL      "subash@example.net"
set_var EASYRSA_REQ_OU         "IT"
```


$ ./easyrsa init-pki
$ ./easyrsa build-ca
$ ./easyrsa gen-req server nopass
$ ./easyrsa sign-req server server
$ ./easyrsa gen-dh
$ openvpn --genkey --secret pki/ta.key

Step 4: Configure OpenVPN
Copying server config
```
sudo cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf  /etc/openvpn/server.conf
```
copying certificates and key
```
sudo cp /home/subash/openvpn-ca/pki/{ca.crt,dh.pem,ta.key} /etc/openvpn
sudo cp /home/subash/openvpn-ca/pki/issued/server.crt /etc/openvpn
sudo cp /home/subash/openvpn-ca/pki/private/server.key /etc/openvpn
```

Now configuring server config of  (```/etc/openvpn/server.conf```)
```
ca ca.crt
cert server.crt
key server.key  
dh dh.pem

;tls-auth ta.key 0
tls-crypt ta.key
```


enable Ip forwarding
```
$ sudo nano /etc/sysctl.conf
# Uncomment the following line:
net.ipv4.ip_forward=1
```
Appplying the changes
```
sudo sysctl -p
```

Step -5 Starting and Enable OpenVPN

```
sudo systemctl start openvpn@server
# Restart the service if it is started already
sudo systemctl restart openvpn@server
```

Enabling the service so that it can start automatic after host turned on
```
sudo systemctl enable openvpn@server
```
