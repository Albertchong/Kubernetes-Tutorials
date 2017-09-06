---
layout: post
title: "Kubernetes Tutorials (12)"
description: "Kube-Dashbboard"
tags: [Kubernetes, Docker]
---

# Kubernetes Tutorials (11)

## Deploy Kube-Dashboard Addon

Create `kube-dashboard-rc.yaml` and `kube-dashboard-svc.yaml`.

kube-dashboard-rc.yaml

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: kubernetes-dashboard-v1.6.0
  namespace: kube-system
  labels:
    k8s-app: kubernetes-dashboard
    version: v1.6.0
    kubernetes.io/cluster-service: "true"
spec:
  replicas: 1
  selector:
    k8s-app: kubernetes-dashboard
  template:
    metadata:
      labels:
        k8s-app: kubernetes-dashboard
        version: v1.6.0
        kubernetes.io/cluster-service: "true"
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ''
        scheduler.alpha.kubernetes.io/tolerations: '[{"key":"CriticalAddonsOnly", "operator":"Exists"}]'
    spec:
      containers:
      - name: kubernetes-dashboard
        image: gcr.io/google_containers/kubernetes-dashboard-amd64:v1.6.0
        resources:
          limits:
            cpu: 100m
            memory: 50Mi
          requests:
            cpu: 100m
            memory: 50Mi
        ports:
        - containerPort: 9090
        livenessProbe:
          httpGet:
            path: /
            port: 9090
          initialDelaySeconds: 30
          timeoutSeconds: 30
```

After that, check for `kubernetes-dashboard` pod up and running:

```bash
kubectl get pod -n kube-system | grep kubernetes-dashboard
```

Here we created the kube-dashboard  ReplicationController.

Now we need to create the related service:

kube-dashboard-svc.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: kubernetes-dashboard
  namespace: kube-system
  labels:
    k8s-app: kubernetes-dashboard
    kubernetes.io/cluster-service: "true"
spec:
  selector:
    k8s-app: kubernetes-dashboard
  ports:
  - port: 80
    targetPort: 9090
  type: NodePort
```

Please be noted , here we added "type: NodePort". If you set the type field to "NodePort", the Kubernetes master will allocate a random port from a flag-configured range (default: 30000-32767), and each Node will proxy that port (the same port number on every Node) into your Service.

Let's check the service

```bash
$ kubectl get svc -n kube-system | grep kubernetes-dashboard
kubernetes-dashboard                        10.3.79.30     <nodes>       80:31145/TCP    81d
```

Here I have a nodeport : 31145, I believe you will get a different one from this. use one of your http://{worknode_ip}:{nodeport} to access your kubernetes dashboard. Replace {worknode_ip} and {nodeport} with your own values.