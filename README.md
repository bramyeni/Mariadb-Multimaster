# Mariadb Multimaster using Galera Cluster
## Pre-requisites
Ensure Donor and Joiner node are able to communicate each other, otherwise see my site-site vpn setup within other repo of my github here

## Assumptions
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
