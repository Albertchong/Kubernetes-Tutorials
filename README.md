# Kubernetes-Tutorials

# Features

## Addons
    Kubernetes Dashboard    
    Kube-DNS 

## Security 
    Calico Network Policy   
    APIServer Auilt Logs        
    RBAC                                  

## Monitoring
    Heapster                           
    InfluxDB                              
    Grafana                             
    Prometheus                      
    AlertManger                     
    Slack Channel Alert       
    Email  Alert                       
    Node-exporter                 

## Logging
    Flunted                               
    ElasticSearch                   
    Kibana                               

## Services
    Traefik Ingress                  

## Package mangerment 
    Helm                                   

## Storage
    CephRBD                           
    CephFS                              
    NFS                                      


# Network Range

*   Node IP :   172.16.164.0/24

*   POD IP:     10.2.0.0/16

*   Cluster IP: 10.3.0.0/16

# Server List

|   ServerName  |   IP Address  |   vCPU    |   Memory(GB)  |   HDisk(GB)   |   OS   |   Descriptions |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|E11K8SCA01|172.16.164.99|2|2|20|CentOS|Certificates Generator & Http File Service|
|E11K8SETCD01|172.16.164.101|8|16|100|CoreOS|ETCD Cluster(Kubernetes Key-Value DataBase)|
|E11K8SETCD02|172.16.164.102|8|16|100|CoreOS|ETCD Cluster(Kubernetes Key-Value DataBase)|
|E11K8SETCD03|172.16.164.103|8|16|100|CoreOS|ETCD Cluster(Kubernetes Key-Value DataBase)|
|E11K8SETCD04|172.16.164.104|8|16|100|CoreOS|ETCD Cluster(Kubernetes Key-Value DataBase)|
|E11K8SETCD05|172.16.164.105|8|16|100|CoreOS|ETCD Cluster(Kubernetes Key-Value DataBase)|
|E11K8SCTL01|172.16.164.107|4|4|40|CentOS|Kubectl & Helm |
|E11K8SHAPX01|172.16.164.108|4|4|60|CentOS|Haproxy & Keepalived (VIP: 172.16.164.110)|
|E11K8SHAPX02|172.16.164.109|4|4|60|CentOS|Haproxy & Keepalived (VIP: 172.16.164.110)|
|E11K8SMA01|172.16.164.111|8|32|200|CoreOS|Kubernetes Master Node|
|E11K8SMA02|172.16.164.112|8|32|200|CoreOS|Kubernetes Master Node|
|E11K8SMA03|172.16.164.113|8|32|200|CoreOS|Kubernetes Master Node|
|E11K8SWK01|172.16.164.121|8|32|200|CoreOS|Kubernetes Worker Node|
|E11K8SWK02|172.16.164.122|8|32|200|CoreOS|Kubernetes Worker Node|
|...|...|...|...|...|...|
|E11K8SWK27|172.16.164.147|8|32|200|CoreOS|Kubernetes Worker Node|





# Kubernetes_Architecture

<img src="images/Kubernetes_Architecture.png" height="1050" width="900">
