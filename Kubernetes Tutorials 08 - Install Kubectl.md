---
layout: post
title: "Kubernetes Tutorials (8)"
description: "How to install the Kubectl  Nodes"
tags: [Kubernetes, Docker]
---

# Kubernetes Tutorials (8)

## How to install the Kubectl 

# Server List 

|   ServerName  |   IP Address  |   vCPU    |   Memory(GB)  |   HDisk(GB)   |   Descriptions |
|:---:|:---:|:---:|:---:|:---:|:---:|
|E11K8SCTL01|172.16.164.107|4|4|40|Kubectl & Helm |


## Step 1: Generate Cluster Admin Certificate

Let's login the CA server as root

|   ServerName  |   IP Address  |   vCPU    |   Memory(GB)  |   HDisk(GB)   |   Descriptions |
|:---:|:---:|:---:|:---:|:---:|:---:|
|E11K8SCA01|172.16.164.99|2|2|20|Certificates Generator & Http File Service|

```
cd ~/root/k8sssl
```

```
openssl genrsa -out admin-key.pem 2048
openssl req -new -key admin-key.pem -out admin.csr -subj "/O=system:masters/CN=kube-admin"
openssl x509 -req -in admin.csr -CA ca.pem -CAkey ca-key.pem -CAcreateserial -out admin.pem -days 3650
```

## Step 2: Copy the certificates to the Kubectl nodes

Login kubectl node as root

|   ServerName  |   IP Address  |   vCPU    |   Memory(GB)  |   HDisk(GB)   |   Descriptions |
|:---:|:---:|:---:|:---:|:---:|:---:|
|E11K8SCTL01|172.16.164.107|4|4|40|Kubectl & Helm |

Create certificate folders

```
mkdir -p /etc/kubernetes/ssl/
```

Download certificates

```
cd /etc/kubernetes/ssl/
curl -O http://172.16.164.99/k8sssl/ca.pem
curl -O http://172.16.164.99/k8sssl/admin.pem
curl -O http://172.16.164.99/k8sssl/admin-key.pem
```

> Replacing `172.16.164.99` with your own CA ip address.

## Step 3: Download and setup the kubectl

Download kubectl binary

```
curl -O https://storage.googleapis.com/kubernetes-release/release/v1.6.3/bin/linux/amd64/kubectl
```

```
chmod +x kubectl
mv kubectl /usr/local/bin/kubectl
```

Setup the cluster

```
kubectl config set-cluster default-cluster --server=https://172.16.164.110 --certificate-authority=/etc/kubernetes/ssl/ca.pem
```

Setup the user

```
kubectl config set-credentials default-admin --certificate-authority=/etc/kubernetes/ssl/ca.pem --client-key=/etc/kubernetes/ssl/admin-key.pem --client-certificate=/etc/kubernetes/ssl/admin.pem
```

Setup the context

```
kubectl config set-context default-system --cluster=default-cluster --user=default-admin
```

Use context

```
kubectl config use-context default-system
```

## Step 4: Confirm the cluster

### check the nodes

```
kubectl get nodes
```

```
[root@e11k8sctl01 ~]# kubectl get nodes
NAME             STATUS                     AGE       VERSION
172.16.164.111   Ready,SchedulingDisabled   43d       v1.6.4+coreos.0
172.16.164.112   Ready,SchedulingDisabled   43d       v1.6.4+coreos.0
172.16.164.113   Ready,SchedulingDisabled   43d       v1.6.4+coreos.0
172.16.164.121   Ready                      42d       v1.6.4+coreos.0
172.16.164.122   Ready                      42d       v1.6.4+coreos.0
172.16.164.123   Ready                      40d       v1.6.4+coreos.0
172.16.164.124   Ready                      40d       v1.6.4+coreos.0
172.16.164.125   Ready                      40d       v1.6.4+coreos.0
172.16.164.126   Ready                      38d       v1.6.4+coreos.0
172.16.164.127   Ready                      38d       v1.6.4+coreos.0
172.16.164.128   Ready                      38d       v1.6.4+coreos.0
172.16.164.129   Ready                      38d       v1.6.4+coreos.0
172.16.164.130   Ready                      38d       v1.6.4+coreos.0
172.16.164.131   Ready                      38d       v1.6.4+coreos.0
172.16.164.132   Ready                      38d       v1.6.4+coreos.0
172.16.164.133   Ready                      38d       v1.6.4+coreos.0
172.16.164.134   Ready                      38d       v1.6.4+coreos.0
172.16.164.135   Ready                      38d       v1.6.4+coreos.0
172.16.164.136   Ready                      38d       v1.6.4+coreos.0
172.16.164.137   Ready                      38d       v1.6.4+coreos.0
172.16.164.138   Ready                      38d       v1.6.4+coreos.0
172.16.164.139   Ready                      38d       v1.6.4+coreos.0
172.16.164.140   Ready                      38d       v1.6.4+coreos.0
172.16.164.141   Ready                      38d       v1.6.4+coreos.0
172.16.164.142   Ready                      38d       v1.6.4+coreos.0
172.16.164.143   Ready                      38d       v1.6.4+coreos.0
172.16.164.144   Ready                      38d       v1.6.4+coreos.0
172.16.164.145   Ready                      38d       v1.6.4+coreos.0
172.16.164.146   Ready                      38d       v1.6.4+coreos.0
172.16.164.147   Ready                      38d       v1.6.4+coreos.0
```

### Check the kube-system pods

```
[root@e11k8sctl01 ~]# kubectl get pod -n kube-system
NAME                                                        READY     STATUS    RESTARTS   AGE
kube-apiserver-172.16.164.111                               1/1       Running   0          5d
kube-apiserver-172.16.164.112                               1/1       Running   0          5d
kube-apiserver-172.16.164.113                               1/1       Running   0          5d
kube-controller-manager-172.16.164.111                      1/1       Running   0          5d
kube-controller-manager-172.16.164.112                      1/1       Running   0          5d
kube-controller-manager-172.16.164.113                      1/1       Running   0          5d
kube-proxy-172.16.164.111                                   1/1       Running   0          5d
kube-proxy-172.16.164.112                                   1/1       Running   0          5d
kube-proxy-172.16.164.113                                   1/1       Running   0          5d
kube-proxy-172.16.164.121                                   1/1       Running   0          5d
kube-proxy-172.16.164.122                                   1/1       Running   0          5d
kube-proxy-172.16.164.123                                   1/1       Running   0          5d
kube-proxy-172.16.164.124                                   1/1       Running   0          5d
kube-proxy-172.16.164.125                                   1/1       Running   0          5d
kube-proxy-172.16.164.126                                   1/1       Running   0          5d
kube-proxy-172.16.164.127                                   1/1       Running   0          5d
kube-proxy-172.16.164.128                                   1/1       Running   0          5d
kube-proxy-172.16.164.129                                   1/1       Running   0          5d
kube-proxy-172.16.164.130                                   1/1       Running   0          5d
kube-proxy-172.16.164.131                                   1/1       Running   0          5d
kube-proxy-172.16.164.132                                   1/1       Running   0          5d
kube-proxy-172.16.164.133                                   1/1       Running   0          5d
kube-proxy-172.16.164.134                                   1/1       Running   0          5d
kube-proxy-172.16.164.135                                   1/1       Running   0          5d
kube-proxy-172.16.164.136                                   1/1       Running   0          5d
kube-proxy-172.16.164.137                                   1/1       Running   0          5d
kube-proxy-172.16.164.138                                   1/1       Running   0          5d
kube-proxy-172.16.164.139                                   1/1       Running   0          5d
kube-proxy-172.16.164.140                                   1/1       Running   0          5d
kube-proxy-172.16.164.141                                   1/1       Running   0          5d
kube-proxy-172.16.164.142                                   1/1       Running   0          5d
kube-proxy-172.16.164.143                                   1/1       Running   0          5d
kube-proxy-172.16.164.144                                   1/1       Running   0          5d
kube-proxy-172.16.164.145                                   1/1       Running   0          5d
kube-proxy-172.16.164.146                                   1/1       Running   0          5d
kube-proxy-172.16.164.147                                   1/1       Running   0          5d
kube-scheduler-172.16.164.111                               1/1       Running   0          5d
kube-scheduler-172.16.164.112                               1/1       Running   0          5d
kube-scheduler-172.16.164.113                               1/1       Running   0          5d
```

> Tips: All these pod was created by the yaml file under the `/etc/kubernetes/manifest`, if something missed, you can check the kubelet logs to find out what is going wrong.
