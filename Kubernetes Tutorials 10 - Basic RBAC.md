---
layout: post
title: "Kubernetes Tutorials (10)"
description: "Basic RBAC"
tags: [Kubernetes, Docker]
---

# Kubernetes Tutorials (10)

## How to Config Basic RBAC

# Server List 

|   ServerName  |   IP Address  |   vCPU    |   Memory(GB)  |   HDisk(GB)   |   Descriptions |
|:---:|:---:|:---:|:---:|:---:|:---:|
|E11K8SCTL01|172.16.164.107|4|4|40|Kubectl & Helm |

## Last toturial, we said we still cannot deploy our applications on the kubernetes for two reasons

    1. calico 
    2. RBAC

We already have the calico deployed in the last tutorial, if everything runs well.

So here, let's deploy two RBAC configuration  to fit the basic need to deploy applications on the Kubernetes.

## What is RBAC

RBAC, aka, Role-Based Access Control (“RBAC”) uses the “rbac.authorization.k8s.io” API group to drive authorization decisions, allowing admins to dynamically configure policies through the Kubernetes API.

As of 1.6 RBAC mode is in beta.

To enable RBAC, start the apiserver with `--authorization-mode=RBAC` .

Go to "Kubernetes Tutorials 6 - Master Nodes installation" and you can see we have enabled it in the `/etc/kubernetes/manifests/kube-apiserver.yaml`.

RBAC is some kind of complicated, if you want to make clear what it means and how it works 

Please see below links for reference:

### [Using RBAC Authorization: https://kubernetes.io/docs/admin/authorization/rbac/](https://kubernetes.io/docs/admin/authorization/rbac/)

And this youtube Video:

[![Kubernetes Auth and Access Control by Eric Chiang, CoreOS](http://img.youtube.com/vi/WvnXemaYQ50/0.jpg)](http://www.youtube.com/watch?v=WvnXemaYQ50&t=1505s)


## Step 1 : Create "addons-cluster-admin" ClusterRoleBinding

Create a "addons-cluster-admin.yaml" file wherever you like.

> Tips: I'd like to suggest you to create a folder named "RBAC", so you can put all your RBAC yaml files in that folder, so you can easily check later.

here is the content of `addons-cluster-admin.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: addons-cluster-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: default
  namespace: kube-system
```
You don't need to change anything. Just use `kubectl` to apply the file:

```bash
kubectl create -f addons-cluster-admin.yaml
```

Using

```bash
 kubectl get clusterrolebindings addons-cluster-admin
``` 

To confirm that we have successfully created the clusterrolebinding.

### What we just did ?

As we know, Kubernetes have two kinds of users. one of them is the "Service Account".

And also as we know, there are namespaces inside the kubernetes cluster , by default, we have the "Default" namespace ,and the "kube-system" namespace where all the system pods, like kube-apiserver, kube-proxy.

You can run :

```bash
kubectl get namespaces
```

to check the namespace you have in your kubernetes cluster.

and 

```bash
Kubectl get pod -n kube-system
``` 
to see all the pods running under the kube-system namespace.

All namespace has a default service account named "Default"

You can run 

```bash
kubectl get serviceaccount -n {namespace}
```

Replace `{namespace}` with your namespace of your cluster. You can try using 'default' and 'kube-system', if you haven't created any. And I guarantee you will see the "default" service account.

What the `addons-cluster-admin.yaml` did is make a `ClusterRoleBinding` to bind the ClusterRole [`cluster-admin`] and [`default`] service account of namespace `kube-system`.

Basically , it give the default service account of the namespace `kube-system` the full access privilege of the whole kubernetes cluster, like "root" privilege for the Linux system.

## Step 2: Create "node-proxier" ClusterRoleBinding

Create a "node-proxier.yaml" file wherever you like.

> Tips: I'd like to suggest you to create a folder named "RBAC", so you can put all your RBAC yaml files in that folder, so you can easily check later.

Here is the content:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: node-proxier
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:node-proxier
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:nodes
```

```bash
kubectl create -f node-proxier.yaml
```

> Tips: If you're not interested about why we need this. You don't need to read below information, you can directly jump to the next chapter to create the kube-DNS.

Remember when we create the certificate for the worker node we used  `-subj "/O=system:nodes/CN=system:node:{{ip address}}"` ?

That's means we give this certificate a group named "system:nodes".

so It can fit almost all the RBAC privileges we need in Kubernetes cluster.

You can run below command to check the ClusterRoleBindings:

```bash
kubectl get clusterrolebindings system:node -o yaml
```

output:

```bash
[root@e11k8sctl01 ~]# kubectl get clusterrolebindings system:node -o yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  creationTimestamp: 2017-05-12T22:34:01Z
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: system:node
  resourceVersion: "67"
  selfLink: /apis/rbac.authorization.k8s.io/v1beta1/clusterrolebindingssystem%3Anode
  uid: 18e8b44f-3763-11e7-af32-000c291bf503
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:node
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:nodes

```

and below command to check the all the privilege the ClusterRole system:node it has:

```bash
kubectl get clusterroles system:node -o yaml
```

output:

```bash
[root@e11k8sctl01 ~]# kubectl get clusterroles system:node -o yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  creationTimestamp: 2017-05-12T22:34:01Z
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: system:node
  resourceVersion: "27"
  selfLink: /apis/rbac.authorization.k8s.io/v1beta1/clusterrolessystem%3Anode
  uid: 18ca44b6-3763-11e7-af32-000c291bf503
rules:
- apiGroups:
  - authentication.k8s.io
  resources:
  - tokenreviews
  verbs:
  - create
- apiGroups:
  - authorization.k8s.io
  resources:
  - localsubjectaccessreviews
  - subjectaccessreviews
  verbs:
  - create
- apiGroups:
  - ""
  resources:
  - services
  verbs:
  - get
  - list
  - watch
- apiGroups:
  - ""
  resources:
  - nodes
  verbs:
  - create
  - get
  - list
  - watch
- apiGroups:
  - ""
  resources:
  - nodes/status
  verbs:
  - patch
  - update
- apiGroups:
  - ""
  resources:
  - nodes
  verbs:
  - delete
  - patch
  - update
- apiGroups:
  - ""
  resources:
  - events
  verbs:
  - create
  - patch
  - update
- apiGroups:
  - ""
  resources:
  - pods
  verbs:
  - get
  - list
  - watch
- apiGroups:
  - ""
  resources:
  - pods
  verbs:
  - create
  - delete
  - get
- apiGroups:
  - ""
  resources:
  - pods/status
  verbs:
  - update
- apiGroups:
  - ""
  resources:
  - configmaps
  - secrets
  verbs:
  - get
- apiGroups:
  - ""
  resources:
  - persistentvolumeclaims
  - persistentvolumes
  verbs:
  - get
- apiGroups:
  - ""
  resources:
  - endpoints
  verbs:
  - get
- apiGroups:
  - certificates.k8s.io
  resources:
  - certificatesigningrequests
  verbs:
  - create
  - get
  - list
  - watch
  ```

  Try to read them and understand them.

  But problem is we missing one rbac roles for the group "system:nodes".

  The "system:node-proxier".

  Try to run below command and compare with the `clusterroles system:node` and see what is the different between them:

  ```bash
  [root@e11k8sctl01 ~]# kubectl get clusterroles system:node-proxier -o yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  creationTimestamp: 2017-05-12T22:34:01Z
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: system:node-proxier
  resourceVersion: "31"
  selfLink: /apis/rbac.authorization.k8s.io/v1beta1/clusterrolessystem%3Anode-proxier
  uid: 18cc6db4-3763-11e7-af32-000c291bf503
rules:
- apiGroups:
  - ""
  resources:
  - endpoints
  - services
  verbs:
  - list
  - watch
- apiGroups:
  - ""
  resources:
  - nodes
  verbs:
  - get
- apiGroups:
  - ""
  resources:
  - events
  verbs:
  - create
  - patch
  - update
  ```

  You can see we only miss a single verb 'list' for resource 'endpoint'.

  And if you can check with below command:

  ```bash
  [root@e11k8sctl01 ~]# kubectl get clusterrolebindings system:node-proxier -o yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  creationTimestamp: 2017-05-12T22:34:01Z
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: system:node-proxier
  resourceVersion: "68"
  selfLink: /apis/rbac.authorization.k8s.io/v1beta1/clusterrolebindingssystem%3Anode-proxier
  uid: 18e933f8-3763-11e7-af32-000c291bf503
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:node-proxier
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: system:kube-proxy
  ```

  You can find out that this `system:node-proxier`  ClusterRole has been assign to the user `system:kube-proxy` but not `system:nodes`.

  But instead create a certificate with `-subj "/O=system:kube-proxy/CN=system:kube-proxy"` for every node's kube-proxy. We just need to assign the ClusterRole `system:node-proxier` to the group `system:nodes`.

  That's why we need this "node-proxier.yaml". Hope you can understand.
