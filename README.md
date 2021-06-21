# Mariadb Multimaster using Galera Cluster
## Pre-requisites
Ensure Donor and Joiner nodes are able to communicate each other, otherwise see my site-site vpn setup within other repo of my github here => https://github.com/bramyeni/openvpn

## Assumptions
<pre>
Cloud Server private IP : 10.2.0.4
Cloud server public IP  : 20.33.100.22
cloud server hostname   : bramdocker.westus.cloudapp.azure.com

local client private IP : 192.168.0.17
local client public IP  : 202.111.222.11
local client hostname   : bramlocal
</pre>

## Diagram 
<pre>
 +-----------------------------------+                                                      
 |             Azure/Cloud Provider  |                                                          
 |    +-------------------------+    |                                                          
 |    |   MariaDB Multimaster   |    |                                                          
 |    |   Donor Node            |    |                                                          
 |    +-------------------------+    |                                                          
 |        Subnet=10.2.0.0/24         |                                                          
 |          +-----------+            |                                                          
 |          |  OpenVPN  |            |                                                          
 |          |  Server   |            |                                                          
 |          +-----|-----+            |                                                          
 |     internal IP : 10.2.0.4        |                                                          
 +----------------|------------------+                                                          
    Cloud PublicIP : 20.33.100.22                                                               
                  |                                                                             
            VPN site-site                                                                       
            using OpenVPN                                                                       
                  |                                                                             
                  |                                                                             
  On-Prem PublicIP : 202.111.222.11                                                             
+-----------------|------------------+                                                          
|      internal IP : 192.168.0.17    |                                                          
|                       +            |                                                          
|             OpenVPN   |            |                                                          
|             Client    |            |                                                          
|                       +            |                                                          
|        Subnet=192.168.0.0/24       |                                                          
|                       -------+     |                                                          
|        MariaDB MultiMaster   |     |                                                          
|        Joiner                |     |                                                          
|                              |     |                                                          
|                       -------+     |                                                          
|                       On-Premise   |                                                          
+------------------------------------+                                                          
</pre>

## Dockerfile
I have created a Dockerfile by pulling base image of Alpine-Edge, this is the smallest image for mariadb galera , yet it has all functionality of galera multimaster

## How to Build the image
Get a dockerfile from this repo, let say you download it into /opt

### For Arch linux base docker container image
<pre>
cd /opt
docker build --tag galera-mariadb -f ./Dockerfile-gmariadb-arch /mnt
</pre>

### For Alpine linux base docker container image
<pre>
docker build --tag galera-mariadb -f ./Dockerfile-gmariadb-alpine /mnt
</pre>

