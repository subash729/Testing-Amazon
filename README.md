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
```
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

```
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

