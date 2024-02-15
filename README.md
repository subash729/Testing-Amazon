# 1. Setup a DNS Server
## 1.1 Create an authoritative DNS server with domain zone interntest.com

Step -1 : Installing and Configuring bind for DNS
```
sudo apt update
sudo apt install bind9
nano /etc/bind/named.conf.local
cat /etc/bind/named.conf.options | grep -i directory


sudo cp /etc/bind/db.local /var/cache/bind/interntest.db
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q1-T1-0-Install-and-copy-local-database.jpg">
</p>

Content Added inside the "named.conf.local"
```bash
zone "interntest.com" {
    type master;
    file "interntest.com.zone";
};
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q1-T1-1-Creating-Zone-file.jpg">
</p>


step -2 : Updating records in zone file of authorative server

Fist finding Ipv4 and Ipv6 of our local system
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q1-T1-3-IP-of-local-machine.jpg">
</p>

```bash
@       IN      SOA     ns1.interntest.com. admin.interntest.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
        IN      NS      interntest.com.
        IN      A       192.168.253.132
        IN      AAAA    fe80::20c:29ff:fee4:fd37

```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q1-T1-2-Auhtorative-server-adding-local-record.jpg">
</p>


Step -3 Checking config and testing authorative server response

```
named-checkconf
named-checkzone interntest.com /var/cache/bind/interntest.db
sudo service  bind9 restart

nslookup interntest.com 192.168.253.132
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q1-T1-4-IP-Checking-restarting-and-testing.jpg">
</p>

## 1.2 Create A record for interntest.com & any CNAME that points to interntest.com

Adding folllowing CNAME record
```
www.interntest.com     IN      CNAME   interntest.com.
```

Command Used
```
 sudo nano /var/cache/bind/interntest.db
 named-checkconf
 named-checkzone interntest.com /var/cache/bind/interntest.db
 sudo service  bind9 restart
 
```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q1-T2-4-Adding-CNAME.jpg">
</p>

Testing the configuration
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q1-T2-CNAME-Test.jpg">
</p>


## 1.3 Listen DNS queries on all interfaces
```
ip address show | grep ": "

sudo ufw allow in on lo to any port 53
sudo ufw allow in on docker0 to any port 53
sudo ufw allow in on vxlan.calico to any port 53
sudo ufw allow in on caliad1f8274a7e@if3 to any port 53
sudo ufw reload
sudo ufw status


sudo tcpdump -i any -n -s 0 -v port 53
```
FInding all interface of server and allow dns traffic by alllowing port 53 and restarting it's service
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q1-T3-Allowing-DNS-on-all-port.jpg">
</p>

Capturing DNS traffic to ensure all interface are allow to recieve dns traffic
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q1-T3-Checking-DNS-trafficon-all-interface.jpg">
</p>

## 1.4 Another VM should be able to get DNS responses from the DNS server for interntest.com domain & other public websites as well using same DNS server

Step -1 COnfiguring options for forwarder and recursion

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q1-T4-COnfgurinf-resolver%20and%20forwarder.jpg">
</p>

Step : 2 COnfiguring nameserver (My previos system was in hostonly so to provide internet  connectivity network range is changed)
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q1-T4-resolve-conf.jpg">
</p>

Step 3 : I had performed test from Windows due to 2 VM were not able share same physical LAN properly simulatnesouly
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q1-T4-resolve-test.jpg">
</p>


# 2 . Design an Nginx load balancer with weighted distribution among backend servers

## 2.1 Create three backend servers: A, B, and C

Step -1 Creating Directory and web-pages of server
```
mkdir -p /var/www/servera
mkdir -p /var/www/serverb
mkdir -p /var/www/serverc

touch /var/www/servera/index.html
touch /var/www/serverb/index.html
touch /var/www/serverc/index.html

nano /var/www/servera/index.html
nano /var/www/serverb/index.html
nano /var/www/serverc/index.html

cp /etc/nginx/sites-enabled/default /etc/nginx/sites-enabled/servera
cp /etc/nginx/sites-enabled/default /etc/nginx/sites-enabled/serverb
cp /etc/nginx/sites-enabled/default /etc/nginx/sites-enabled/serverc
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q2-T1-Creating-directory%20for%20hosting.jpg">
</p>


Step -2 : HOsting multiple Web into single host by using different port
### used config

server {
        listen 1900 ;
        listen [::]:1900;
        root /var/www/servera;
        index index.html index.htm;
        server_name servera;
 location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

}

Testing all multiple web hosted on different port

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q2-T1-server-hosting-config.jpg">
</p>



## 2.2 *Design an Nginx load balancer with weighted distribution among backend servers*
   - Create three backend servers: A, B, and C
   - Each backend server should have a weight assigned based on its capacity
      - Server A has a weight of 2
      - Server B has a weight of 1
      - Server C has a weight of 3

Step -1 Adding mutiple address of webserver for load balancing
```
http {
    upstream backend {
        server 192.168.253.133:1900 weight=2;
        server 192.168.253.133:2000 weight=1;
        server 192.168.253.133:2100 weight=3;
    }
```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q2-T2-Assign%20weight.jpg">
</p>


Step -2 Adding config in individual server created for load-balancing
```
  location / {
                 proxy_pass http://backend;

        }

```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-15-system-network-narayan-subash729/blob/main/materials/Q2-T2-Adding-weight.jpg">
</p>

I am still resarching about it to loadbalance it properly. Time was conused due to nginx and apache2 services was confilcted on my system

