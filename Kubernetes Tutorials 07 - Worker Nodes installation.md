---
layout: post
title: "Kubernetes Tutorials (7)"
description: "How to Install Kubernetes Worker Nodes"
tags: [Kubernetes, Docker]
---

# Kubernetes Tutorials (7)

## How to Install Kubernetes Worker Nodes

# Server List 

|   ServerName  |   IP Address  |   vCPU    |   Memory(GB)  |   HDisk(GB)   |   Descriptions |
|:---:|:---:|:---:|:---:|:---:|:---:|
|E11K8SWK01|172.16.164.121|8|32|200|Kubernetes Worker Node|
|E11K8SWK02|172.16.164.122|8|32|200|Kubernetes Worker Node|
|...|...|...|...|...|...|
|E11K8SWK27|172.16.164.147|8|32|200|Kubernetes Worker Node|

# Step by Step 

## Step 1: Create worker-openssl.cnf

Let's login the CA server as root

|   ServerName  |   IP Address  |   vCPU    |   Memory(GB)  |   HDisk(GB)   |   Descriptions |
|:---:|:---:|:---:|:---:|:---:|:---:|
|E11K8SCA01|172.16.164.99|2|2|20|Certificates Generator & Http File Service|

```
cd ~/root/k8sssl
```

Create worker-openssl.cnf

```
[req]
req_extensions = v3_req
distinguished_name = req_distinguished_name
[req_distinguished_name]
[ v3_req ]
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment
subjectAltName = @alt_names
[alt_names]
IP.1 = $ENV::WORKER_IP
```

## Step 2: Generate the Worker certificate key pair

```
openssl genrsa -out e11k8swk01-worker-key.pem 2048
WORKER_IP=172.16.164.111 openssl req -new -key e11k8swk01-worker-key.pem -out e11k8swk01-worker.csr -subj "/O=system:nodes/CN=system:node:172.16.164.111" -config worker-openssl.cnf
WORKER_IP=172.16.164.111 openssl x509 -req -in e11k8swk01-worker.csr -CA ca.pem -CAkey ca-key.pem -CAcreateserial -out e11k8swk01-worker.pem -days 3650 -extensions v3_req -extfile worker-openssl.cnf
```

> Replacing all the server name `e11k8swk01` and ip address `172.16.164.111` with your own value.

> Repeat above step for all you worker nodes. Make sure the server names and the ip addresses are correct.

> For exmaple:

```
[root@e11k8sca01 k8sssl]# ls
admin.csr                  e11k8swk02-worker.pem      e11k8swk07-worker-key.pem  e11k8swk12-worker.csr      e11k8swk16-worker.pem      e11k8swk21-worker-key.pem  e11k8swk26-worker.csr
admin-key.pem              e11k8swk03-worker.csr      e11k8swk07-worker.pem      e11k8swk12-worker-key.pem  e11k8swk17-worker.csr      e11k8swk21-worker.pem      e11k8swk26-worker-key.pem
admin.pem                  e11k8swk03-worker-key.pem  e11k8swk08-worker.csr      e11k8swk12-worker.pem      e11k8swk17-worker-key.pem  e11k8swk22-worker.csr      e11k8swk26-worker.pem
apiserver.csr              e11k8swk03-worker.pem      e11k8swk08-worker-key.pem  e11k8swk13-worker.csr      e11k8swk17-worker.pem      e11k8swk22-worker-key.pem  e11k8swk27-worker.csr
apiserver-key.pem          e11k8swk04-worker.csr      e11k8swk08-worker.pem      e11k8swk13-worker-key.pem  e11k8swk18-worker.csr      e11k8swk22-worker.pem      e11k8swk27-worker-key.pem
apiserver.pem              e11k8swk04-worker-key.pem  e11k8swk09-worker.csr      e11k8swk13-worker.pem      e11k8swk18-worker-key.pem  e11k8swk23-worker.csr      e11k8swk27-worker.pem
ca-key.pem                 e11k8swk04-worker.pem      e11k8swk09-worker-key.pem  e11k8swk14-worker.csr      e11k8swk18-worker.pem      e11k8swk23-worker-key.pem  openssl.cnf
ca.pem                     e11k8swk05-worker.csr      e11k8swk09-worker.pem      e11k8swk14-worker-key.pem  e11k8swk19-worker.csr      e11k8swk23-worker.pem      worker-openssl.cnf
ca.srl                     e11k8swk05-worker-key.pem  e11k8swk10-worker.csr      e11k8swk14-worker.pem      e11k8swk19-worker-key.pem  e11k8swk24-worker.csr      worker.sh
e11k8swk01-worker.csr      e11k8swk05-worker.pem      e11k8swk10-worker-key.pem  e11k8swk15-worker.csr      e11k8swk19-worker.pem      e11k8swk24-worker-key.pem
e11k8swk01-worker-key.pem  e11k8swk06-worker.csr      e11k8swk10-worker.pem      e11k8swk15-worker-key.pem  e11k8swk20-worker.csr      e11k8swk24-worker.pem
e11k8swk01-worker.pem      e11k8swk06-worker-key.pem  e11k8swk11-worker.csr      e11k8swk15-worker.pem      e11k8swk20-worker-key.pem  e11k8swk25-worker.csr
e11k8swk02-worker.csr      e11k8swk06-worker.pem      e11k8swk11-worker-key.pem  e11k8swk16-worker.csr      e11k8swk20-worker.pem      e11k8swk25-worker-key.pem
e11k8swk02-worker-key.pem  e11k8swk07-worker.csr      e11k8swk11-worker.pem      e11k8swk16-worker-key.pem  e11k8swk21-worker.csr      e11k8swk25-worker.pem

```
## Step 3: Install Worker Nodes with CoreOS.

## Step 4: Copy the certificates to the worker nodes

Login 

|   ServerName  |   IP Address  |   vCPU    |   Memory(GB)  |   HDisk(GB)   |   Descriptions |
|:---:|:---:|:---:|:---:|:---:|:---:|
|E11K8SWK01|172.16.164.121|8|32|200|Kubernetes Worker Node|
|E11K8SWK02|172.16.164.122|8|32|200|Kubernetes Worker Node|
|...|...|...|...|...|...|
|E11K8SWK27|172.16.164.147|8|32|200|Kubernetes Worker Node|

create /etc/kubernetes/ssl folder

```
sudo mkdir -p /etc/kubernetes/ssl
```

Download ca and etcd certificates

```
sudo curl -O http://172.16.164.99/k8sssl/ca.pem
sudo curl -O http://172.16.164.99/cfssl/newegg-etcd-root-ca.pem
sudo curl -O http://172.16.164.99/cfssl/client.pem
sudo curl -O http://172.16.164.99/cfssl/client-key.pem
```
Download Worker nodes certificates

```
WORKER_FQDN=e11k8swk01

sudo curl -O http://172.16.164.99/k8sssl/${WORKER_FQDN}-worker.pem
sudo curl -O http://172.16.164.99/k8sssl/${WORKER_FQDN}-worker-key.pem

sudo ln -s ${WORKER_FQDN}-worker.pem worker.pem
sudo ln -s ${WORKER_FQDN}-worker-key.pem worker-key.pem
```

> Replacing `172.16.164.99` with your own CA ip address, and replacing `e11k8swk01` with your own worker node name.

> Repeat above step on all your worker nodes.

## Step 5: Create configuration files folders

```
sudo mkdir -p /etc/flannel
sudo mkdir -p /etc/systemd/system/flanneld.service.d
sudo mkdir -p /etc/kubernetes/cni
sudo mkdir -p /etc/kubernetes/cni/net.d
sudo mkdir -p /etc/kubernetes/manifests
sudo mkdir -p /etc/systemd/system/docker.service.d
``` 

## Step 6: Flannel configuration

### /etc/flannel/options.env

```
FLANNELD_IFACE=172.16.164.121
FLANNELD_ETCD_ENDPOINTS=https://172.16.164.101:2379,https://172.16.164.101:2379,https://172.16.164.101:2379,https://172.16.164.104:2379,https://172.16.164.105:2379
ETCD_SSL_DIR=/etc/kubernetes/ssl
FLANNELD_ETCD_CAFILE=/etc/kubernetes/ssl/newegg-etcd-root-ca.pem
FLANNELD_ETCD_CERTFILE=/etc/kubernetes/ssl/client.pem
FLANNELD_ETCD_KEYFILE=/etc/kubernetes/ssl/client-key.pem
``` 

> Replacing the `FLANNELD_IFACE` with your real ip address of your current master node.

> Replacing `FLANNELD_ETCD_ENDPOINTS` with your etcd cluster endpoints list.

### /etc/systemd/system/flanneld.service.d/40-ExecStartPre-symlink.conf

```
[Service]
ExecStartPre=/usr/bin/ln -sf /etc/flannel/options.env /run/flannel/options.env
```

## Step 7: Docker Configurations

### /etc/systemd/system/docker.service.d/40-flannel.conf

```
[Unit]
Requires=flanneld.service
After=flanneld.service
[Service]
EnvironmentFile=/etc/kubernetes/cni/docker_opts_cni.env
```

### /etc/kubernetes/cni/docker_opts_cni.env

```
DOCKER_OPT_BIP=""
DOCKER_OPT_IPMASQ=""
```

### /etc/kubernetes/cni/net.d/10-flannel.conf

```
{
    "name": "podnet",
    "type": "flannel",
    "delegate": {
        "isDefaultGateway": true
    }
}
```

## Step 8: Kubelet Service

### /etc/systemd/system/kubelet.service

```
[Service]
Environment=KUBELET_IMAGE_TAG=v1.6.3_coreos.0
Environment="RKT_RUN_ARGS=--uuid-file-save=/var/run/kubelet-pod.uuid \
  --volume var-log,kind=host,source=/var/log \
  --mount volume=var-log,target=/var/log \
  --volume dns,kind=host,source=/etc/resolv.conf \
  --mount volume=dns,target=/etc/resolv.conf \
  --volume cni-bin,kind=host,source=/opt/cni/bin \
  --mount volume=cni-bin,target=/opt/cni/bin \
  --volume modprobe,kind=host,source=/usr/sbin/modprobe \
  --mount volume=modprobe,target=/usr/sbin/modprobe \
  --volume lib-modules,kind=host,source=/lib/modules \
  --mount volume=lib-modules,target=/lib/modules \
  --volume iscsiadm,kind=host,source=/usr/sbin/iscsiadm \
  --mount volume=iscsiadm,target=/usr/sbin/iscsiadm"
ExecStartPre=/usr/bin/mkdir -p /etc/kubernetes/manifests
ExecStartPre=/usr/bin/mkdir -p /var/log/containers
ExecStartPre=-/usr/bin/rkt rm --uuid-file=/var/run/kubelet-pod.uuid
ExecStartPre=/usr/bin/mkdir -p /opt/cni/bin
ExecStart=/usr/lib/coreos/kubelet-wrapper \
  --api-servers=https://172.16.164.110 \
  --cni-conf-dir=/etc/kubernetes/cni/net.d \
  --network-plugin=cni \
  --container-runtime=docker \
  --register-node=true \
  --allow-privileged=true \
  --pod-manifest-path=/etc/kubernetes/manifests \
  --hostname-override=172.16.164.121 \
  --cluster_dns=10.3.0.10 \
  --cluster_domain=cluster.local \
  --kubeconfig=/etc/kubernetes/worker-kubeconfig.yaml \
  --tls-cert-file=/etc/kubernetes/ssl/worker.pem \
  --tls-private-key-file=/etc/kubernetes/ssl/worker-key.pem
ExecStop=-/usr/bin/rkt stop --uuid-file=/var/run/kubelet-pod.uuid
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

> Replacing `--hostname-override=172.16.164.121` with your current worker node ip

> Replacing `--api-servers=https://172.16.164.110` with your APIServers VIP address.

> Replacing `KUBELET_IMAGE_TAG=v1.6.3_coreos.0` with your favor value , for more detail you can check the Link: [https://quay.io/repository/coreos/hyperkube?tab=tags](https://quay.io/repository/coreos/hyperkube?tab=tags)

## Step 9: Kube Proxy

### /etc/kubernetes/manifests/kube-proxy.yaml

```
apiVersion: v1
kind: Pod
metadata:
  name: kube-proxy
  namespace: kube-system
spec:
  hostNetwork: true
  containers:
  - name: kube-proxy
    image: quay.io/coreos/hyperkube:v1.6.3_coreos.0
    command:
    - /hyperkube
    - proxy
    - --master=https://172.16.164.110
    - --kubeconfig=/etc/kubernetes/worker-kubeconfig.yaml
    securityContext:
      privileged: true
    volumeMounts:
    - mountPath: /etc/ssl/certs
      name: "ssl-certs"
    - mountPath: /etc/kubernetes/worker-kubeconfig.yaml
      name: "kubeconfig"
      readOnly: true
    - mountPath: /etc/kubernetes/ssl
      name: "etc-kube-ssl"
      readOnly: true
  volumes:
  - name: "ssl-certs"
    hostPath:
      path: "/usr/share/ca-certificates"
  - name: "kubeconfig"
    hostPath:
      path: "/etc/kubernetes/worker-kubeconfig.yaml"
  - name: "etc-kube-ssl"
    hostPath:
      path: "/etc/kubernetes/ssl"
```

> Replacing `--master=https://172.16.164.110` with your APIServers VIP address.

### /etc/kubernetes/worker-kubeconfig.yaml

```
apiVersion: v1
kind: Config
clusters:
- name: local
  cluster:
    certificate-authority: /etc/kubernetes/ssl/ca.pem
users:
- name: kubelet
  user:
    client-certificate: /etc/kubernetes/ssl/worker.pem
    client-key: /etc/kubernetes/ssl/worker-key.pem
contexts:
- context:
    cluster: local
    user: kubelet
  name: kubelet-context
current-context: kubelet-context
```

## Step 10: Start the Services

### Daemon Reload

```
sudo systemctl daemon-reload
```

### Start flanneld

```
sudo systemctl enable flanneld --now
```

### Start kubelet

```
sudo systemctl enable kubelet --now
```

## Step 11: Basic Check

Check command 1:

```
core@e11k8swk01 ~ $ curl -s localhost:10255/pods | jq -r '.items[].metadata.name'
```

Output:

```
kube-proxy-172.16.164.121
```

