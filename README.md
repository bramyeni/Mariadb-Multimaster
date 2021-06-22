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


##How to run the docker image

for single node:
Rebuild DBCONFIG
<pre>
docker run -it --name galera-mariadb --network host -v /opt/mariadb:/var/lib/mysql -v /opt/mariadb/conf:/etc/my.cnf.d -e MARIADB_DBCONFIG_REBUILD=yes galera-mariadb
</pre>

Rebuild DBCONFIG and Database
<pre>
docker run -it --name galera-mariadb --network host -v /opt/mariadb:/var/lib/mysql -v /opt/mariadb/conf:/etc/my.cnf.d -e MARIADB_DBCONFIG_REBUILD=yes -e MARIADB_DATABASE_REBUILD=yes galera-mariadb
</pre>

Run without Rebuild
<pre>
docker run -it --name galera-mariadb --network host -v /opt/mariadb:/var/lib/mysql -v /opt/mariadb/conf:/etc/my.cnf.d galera-mariadb
</pre>
or

Rebuild DBCONFIG and DATABASE

<pre>
docker run -it --name galera-mariadb --network host -v /opt/mariadb:/var/lib/mysql -v /opt/mariadb/conf:/etc/my.cnf.d -e MARIADB_DBCONFIG_REBUILD=yes -e MARIADB_DATABASE_REBUILD=yes galera-mariadb
</pre>

for donor node:
Rebuild DBCONFIG with HOST network
<pre>
docker run -it --name galera-mariadb  --network host -v /opt/mariadb:/var/lib/mysql -v /opt/mariadb/conf:/etc/my.cnf.d -e GALERA_CLUSTER_ACTIVE=yes -e MARIADB_DBCONFIG_REBUILD=yes galera-mariadb
</pre>

Rebuild DBCONFIG and Database
<pre>
docker run -it --name galera-mariadb  --network host -v /opt/mariadb:/var/lib/mysql -v /opt/mariadb/conf:/etc/my.cnf.d -e GALERA_CLUSTER_ACTIVE=yes -e MARIADB_DBCONFIG_REBUILD=yes -e MARIADB_DATABASE_REBUILD=yes galera-mariadb
</pre>

Run without Rebuild with HOST network
<pre>
docker run -it --name galera-mariadb  --network host -v /opt/mariadb:/var/lib/mysql -v /opt/mariadb/conf:/etc/my.cnf.d -e GALERA_CLUSTER_ACTIVE=yes galera-mariadb
</pre>
for joiner node:
Rebuild DBCONFIG and DATABASE with HOST network
<pre>
docker run -it --name galera-mariadb  --network host -v /opt/mariadb:/var/lib/mysql -v /opt/mariadb/conf:/etc/my.cnf.d -e MARIADB_DBCONFIG_REBUILD=yes -e MARIADB_DATABASE_REBUILD=yes -e GALERA_CLUSTER_ADDRESS="gcomm://192.168.0.158" galera-mariadb
</pre>

Run without Rebuild DBCONFIG and DATABASE with HOST network
<pre>
docker run -it --name galera-mariadb  --network host -v /opt/mariadb:/var/lib/mysql -v /opt/mariadb/conf:/etc/my.cnf.d -e GALERA_CLUSTER_ADDRESS="gcomm://192.168.0.158" galera-mariadb
</pre>

NOTE:
<pre>
/opt/mariadb (on Host)      is mapped to /var/lib/mysql (on Container)
/opt/mariadb/conf (on Host) is mapped to /etc/my.cnf.d (on Container)
/opt/web                    is mapped to Apache2 dir
/opt/web/main               is mapped to Apache2 document root
MARIADB_DBCONFIG_REBUILD=yes (re-create /etc/my.cnf.d/server.cnf
MARIADB_DATABASE_REBUILD=yes (destroy and re-create database under directory /var/lib/mysql/data )
--network host (it uses host network, NOTE: galera must use network host if it requires to have multimaster replication on a different node)

</pre>
