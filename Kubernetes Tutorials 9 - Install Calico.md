---
layout: post
title: "Kubernetes Tutorials (9)"
description: "How to install Calico"
tags: [Kubernetes, Docker]
---

# Kubernetes Tutorials (9)

## How to install Calico

# Server List 

|   ServerName  |   IP Address  |   vCPU    |   Memory(GB)  |   HDisk(GB)   |   Descriptions |
|:---:|:---:|:---:|:---:|:---:|:---:|
|E11K8SCTL01|172.16.164.107|4|4|40|Kubectl & Helm |

## Until now, we still cannot deploy our applications on the kubernetes for two reasons

    1. calico 
    2. RBAC

SO let's fix the calico first.

## Step 1: Encrypt the etcd certificates for Calico configmap

Let's login the CA server as root

|   ServerName  |   IP Address  |   vCPU    |   Memory(GB)  |   HDisk(GB)   |   Descriptions |
|:---:|:---:|:---:|:---:|:---:|:---:|
|E11K8SCA01|172.16.164.99|2|2|20|Certificates Generator & Http File Service|

```
cd ~/root/k8sssl
```

Run Below message to get the base64 encrypted etcd certs value.

For the  etcd CA cert

```
cat newegg-etcd-root-ca.pem|base64 -w0
```

The Output would be like below:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUR5RENDQXJDZ0F3SUJBZ0lVR3dGczVkb1BkcXBRbjZsRTF0dUN3OCtJc0s4d0RRWUpLb1pJaHZjTkFRRUwKQlFBd2FqRUxNQWtHQTFVRUJoTUNWVk14RkRBU0JnTlZCQWdUQzB4dmN5QkJibWRsYkdWek1Rc3dDUVlEVlFRSApFd0pEUVRFVU1CSUdBMVVFQ2hNTFRrVlhSVWRISUVsT1F5NHhEREFLQmdOVkJBc1RBMFV4TVRFVU1CSUdBMVVFCkF4TUxUbVYzWldkbklFVlVRMFF3SGhjTk1UY3dOakl3TVRJd05EQXdXaGNOTWpJd05qRTVNVEl3TkRBd1dqQnEKTVFzd0NRWURWUVFHRXdKVlV6RVVNQklHQTFVRUNCTUxURzl6SUVGdVoyVnNaWE14Q3pBSkJnTlZCQWNUQWtOQgpNUlF3RWdZRFZRUUtFd3RPUlZkRlIwY2dTVTVETGpFTU1Bb0dBMVVFQ3hNRFJURXhNUlF3RWdZRFZRUURFd3RPClpYZGxaMmNnUlZSRFJEQ0NBU0l3RFFZSktvWklodmNOQVFFQkJRQURnZ0VQQURDQ0FRb0NnZ0VCQUtDczFmSGEKWnVkaWx3aEU3SGpoTE5GdHk0WEtZN3hmbWJoeTlxeEdzdnAvMzBEa3dUOFdoZTZtSytCcEp4SlJoVWcveWRSbwpRSWsyNWd1SHNFSjlRS2k4SEh0c1BFbXh1R0FkZXk0ajJVSWUzcFpJWWx6dGpNSFRPR2w4WTBTZDdDc1JwSVU3CllDeXUyejYxaEdMcks4c2xIRkJ3OXpDK2hnSDQ1eWV1LzhwYUJLbm1RREc0RjA4V1NReTEwYVBldy95NFBvcm4KWU4rbmh4UjFpSThjTGM5dFZDSEtQQk9EakRlQkxNZzBmNnBpaHF5ZjFXZ1V4eFZwY25Xa0hJVlpzZ1lMMHZLZAo4SC9VcG9BUnlvZU9sZCtHRlRHZ3VFc1F4bDR1aUR6V2V3aWxiTWg5LzVHd2RSbGVJOHZDdHd5R21VSkdxN3VKCnlBWVR5UEphT2pMNXFRTUNBd0VBQWFObU1HUXdEZ1lEVlIwUEFRSC9CQVFEQWdFR01CSUdBMVVkRXdFQi93UUkKTUFZQkFmOENBUUl3SFFZRFZSME9CQllFRk8vL3YrRFNYcy9ldzlwUzd0d1NoQUp2c1NWQk1COEdBMVVkSXdRWQpNQmFBRk8vL3YrRFNYcy9ldzlwUzd0d1NoQUp2c1NWQk1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQ0U0Rkl1CmxRNDZTZXhyWEtCVkcrNGRYcUpHK1cvVFUxeEJjKzNTeEdlQmFLc0xRVmRjY0FOckZtY0U3Sm5Sb0FzZXk2S0oKZXRVc0N6OGZaSHhESzFEaHJ6Tm9EdU0wcnppUEdnNmpIclBXdUVJRHNQTW0rNzUxNXZIc0pHN1NDNzdmdGZCWAplZ1pGdHNPZyswVVdOcGJ4YlhyQXR1V0dCTyt5cDZMcjZEL2VGa3VtVTRQQmQ3S2pvNmJLVEtsek5handKcGI0CkxXZjZoSEJqYm5QNDRqMk9DeWRMYWJZMkcvSFN6ZzJZVlVtTllPZ2JneG0wM2RXbWpDTHFDNVRPYndLVHlsdUoKUDUvUDZSZDM2VnVIMllaRHhhdEtvWGlYaGp3aUwxbVR4NytPS0RjRXVzRERKZUtnZ2ZRSmQzbFRQcXdrVmFXegpIb2UxVzlSdDFHZFhpcG45Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
```

For the  client cert

```
cat client.pem|base64 -w0
```

The Output would be like below:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURqVENDQW5XZ0F3SUJBZ0lVTmcwYlU4M3RlWEdhejdqWFd4R0hJT1M3eGtvd0RRWUpLb1pJaHZjTkFRRUwKQlFBd2FqRUxNQWtHQTFVRUJoTUNWVk14RkRBU0JnTlZCQWdUQzB4dmN5QkJibWRsYkdWek1Rc3dDUVlEVlFRSApFd0pEUVRFVU1CSUdBMVVFQ2hNTFRrVlhSVWRISUVsT1F5NHhEREFLQmdOVkJBc1RBMFV4TVRFVU1CSUdBMVVFCkF4TUxUbVYzWldkbklFVlVRMFF3SGhjTk1UY3dOakl3TVRJd09EQXdXaGNOTWpjd05qRTRNVEl3T0RBd1dqQVIKTVE4d0RRWURWUVFERXdaamJHbGxiblF3Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLQW9JQgpBUUROM0RTM20zamlhZ1Qvc0dBZkR6RERZWUFRTTZSK3lyVzY3VFFCL0RmenJEMDVtK1A3TmNmeHdoSi90Yk9oCjZsRGoxeGxnRFE2aVlKdlFWRWV0NC9KelVDK01qU29GVnBDd1R3cm5YNVFocGJja0wzMEU1VFJybGl1eXlldGwKb1B1d2EvRytKbTZ5cU04cDNRQmRNdjExOWdCRCtxdk5rNjNIbW5vSXdVRG1SYXRJamlqS0Voa1VoRjFPcGowUwpPZXNSNlJDREo2MkZkZnBOQU92cXU4QjJJeEtlTENaWC9xWjFnK0pKZmhSclcvZk00dTlTNjJCTjJpQ3h0RmMyClRTd21EQjRtZHJKOFRjT1Bod1lldWM5bmsxMG4weVpyTHI2cjM4WTRMKzZVZGg4dTJaRmVWMGxuRTdNYnhIMSsKYmpQQnREK2pWTE9QbTB2ZU1lUzlxQUJIQWdNQkFBR2pnWU13Z1lBd0RnWURWUjBQQVFIL0JBUURBZ1dnTUJNRwpBMVVkSlFRTU1Bb0dDQ3NHQVFVRkJ3TUNNQXdHQTFVZEV3RUIvd1FDTUFBd0hRWURWUjBPQkJZRUZPZ0hwUnhzCllieUYvbzhrNEIwaGFObGJHZVFYTUI4R0ExVWRJd1FZTUJhQUZPLy92K0RTWHMvZXc5cFM3dHdTaEFKdnNTVkIKTUFzR0ExVWRFUVFFTUFLQ0FEQU5CZ2txaGtpRzl3MEJBUXNGQUFPQ0FRRUFjK0t6a0tNT29UekIxdjMvVXJDdApaN1kwUU42OHN3Tjh2TmVDeFMvajVLQ0llSlZvZ0dwVzR3ZmYvQjA0bXhRTUw2RFZlWi96bjFNelNVZEJZMU5QCko4UWF4SzREYndqSnpMMHk2blBDQXNXMG40NGoyKys2Y2NMdzIwUFA4Z0xHeXVhM1EyM2IwVElyVVYwNTEwcHYKbDkrSVZoaU52Y3RoT0JxQjd1cWFVU2pxcXdRUmJnbHBBeW4yYytSNGVGRXJhTXBMMFRYQkVmYXZXWE1SRG02QgphZGZVQ2wyamgxZU1wY2hRVmlHNHdoTGxYT2RDbkVTQTNzVUdWbnRXZmk5MG05QUVEa3RpUktmQVJzRjRhOW5HCkF6YndjaVVBbjRYT2pYT1oyNHlqM2lHUy96QzlzbVZXL2N4MkpjbVVXT3A1M1FwNjNIcU9OeTAweU5nL0ptR1UKemc9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
```

For the  client key

```
cat client-key.pem|base64 -w0
```

The Output would be like below:

```
LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFcEFJQkFBS0NBUUVBemR3MHQ1dDQ0bW9FLzdCZ0h3OHd3MkdBRURPa2ZzcTF1dTAwQWZ3Mzg2dzlPWnZqCit6WEg4Y0lTZjdXem9lcFE0OWNaWUEwT29tQ2IwRlJIcmVQeWMxQXZqSTBxQlZhUXNFOEs1MStVSWFXM0pDOTkKQk9VMGE1WXJzc25yWmFEN3NHdnh2aVp1c3FqUEtkMEFYVEw5ZGZZQVEvcXJ6Wk90eDVwNkNNRkE1a1dyU0k0bwp5aElaRklSZFRxWTlFam5yRWVrUWd5ZXRoWFg2VFFEcjZydkFkaU1Tbml3bVYvNm1kWVBpU1g0VWExdjN6T0x2ClV1dGdUZG9nc2JSWE5rMHNKZ3dlSm5heWZFM0RqNGNHSHJuUFo1TmRKOU1tYXk2K3E5L0dPQy91bEhZZkx0bVIKWGxkSlp4T3pHOFI5Zm00endiUS9vMVN6ajV0TDNqSGt2YWdBUndJREFRQUJBb0lCQVFERUFObFV4RHY1MzVYNAoyVGI3KzV1ak82OFNQZ2x1QzVXeWIrem53dnlIRHNTUW1HUWVLRnZDVE5VT01iUGlBMWFYU0pOSmg4VWFSNyt6ClFMdXF6bkZtNTc4T0pNNFFuUlpRT0VJYWdEOXJOZnhGQU00WkhoNm1IU3BvSmFieWNhK1NqNFJ4VnVocFJSOVMKTGJFT0MrR1dNZTZZeXhCUFFUL2gzNnczcjhFYVBSM1NNa0w4bUtVd1dnSkJzSXIwOFZsNU80RXd2dEdiWXhmOQpHWTZpYlkxa3VJamQ4QTNVUVU1ZElnc1JnelJBOXVwVXdwRCtEeVNrNWdSSlZzZ2pjREljRG91OWxNSmIrTDFnClFuS0I1eklwUzl5N3VSamI4SW1VUDJSYytmNlZNMHAyRTRGWWZCOUt4Z3JsSldOZFBqZW5HUTdYVHZPQWJKNzUKNTBKZlJNMTVBb0dCQVA2bExxNCtPNEZMU3ovblpWY2o0OWRielNNb2x6RVZhenR4Z3VacVlZRXlUdENjWU1KdApLdnYvME9yalkzeU9DR0VwYjYwRTB4QW9xU2gzS1lFK21LQzZrNzhwQlJwSTBqZTNFMW9Rb1BzcVZsNnk0bkFtCm9ieDU1eVRxbUd2aThuN3prWlczcnhlRnBYdFpmeUd3SkdCNElPV2ZuZGFtOGFtYWxxK0FiK1QxQW9HQkFNNzAKbElBVjJ6aTRTM3JXZmpldnErRzlLM2hsSExxYnFwMkNxd2hVQWFYQWF3ZjdTTENPcUJYT3NBUUtkbHFGZHNKUwpET1V5aVh3K2lOMFduSGlYUVZIcm9CNC8vNmNHWndvMitBMTNhclI1Z1h1djZwRmx0T2FCNXlKTDVWODk1Z0lqCnFxVTZxVnhrZUtUWjQzdnprUXdqN2xram53NWZhYW43cTNnN29HckxBb0dBUFRaajlFck5ieFUwYjVZbVRENDYKWk9pU2RXTXFoUWdMRU8wR2RIRWFJejUrcDdNUHAyMHFxZzBpb1hRcjFDTW9iSUxUYjVuN1NxVWJsTGlnWjRzdQprV1NJWTZsdXFwVGF3NzNranYvMFVxd291dmJ4YTlnOWhOc2c3MFRkZ3dHNUhUOWJ2Y3U3OFVHVk9BT1poZldiCnFQejNtM3MxTEFaUmpWMjEzMVpXajRrQ2dZQVhkbmNYaG1MU0xXS3hUSHdHeUMzT2ErbnZ2bGJoZVNDQXR2MjQKOWYweDAzek83Tmd5M0JtR1QyWjVMczU0T01WY0JuMWJ4cno3andOcFBWVUQvdkh5M2FuTGhDbnh2N0lTNEdTQwo4bFR3blEzbS9leTBDem5WSUVrYUJFeSszWCtrSW5NSVJIeEs4MEVTUDNtZzVkNDk2Qnp2a2pCSUt0MEozblIwCkx4NWl2UUtCZ1FESm5wc0hiby9vZkRqYllOSXBFV0RGK2N1clRPakJxTmwyZlp1R1VZNWZST3B5c1lsQnhkdS8KNlJEQXBCSDZyYWw4NjBVUjMwSDN0eUtaRWlYQU5xYjhYREE1eGQvV3g5YVBxUWJtSldOWDdpSEZ1bC9KOTJHQwpQeS8vR1hBeXIxZ2R1ZytFQXZlNm1scTlGT2RiN0YvSU5LMmlDcTlsZGkzQWJSL3N6QTJsVVE9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
```

Copy and Keep these values safe and we will use them in the later calico.yaml file.

## Step 2: Login the kubectl node 

Let's login as root

|   ServerName  |   IP Address  |   vCPU    |   Memory(GB)  |   HDisk(GB)   |   Descriptions |
|:---:|:---:|:---:|:---:|:---:|:---:|
|E11K8SCTL01|172.16.164.107|4|4|40|Kubectl & Helm |


## Step 3: Create calico.yaml file

```
mkdir -p /root/kubernetes/calico/

cd  /root/kubernetes/calico/

vi calico.yaml
```

Paste below content

```
# This ConfigMap is used to configure a self-hosted Calico installation.
kind: ConfigMap
apiVersion: v1
metadata:
  name: calico-config 
  namespace: kube-system
data:
  # Configure this with the location of your etcd cluster.
  etcd_endpoints: "https://172.16.164.101:2379,https://172.16.164.102:2379,https://172.16.164.103:2379,https://172.16.164.104:2379,https://172.16.164.105:2379"

  # The CNI network configuration to install on each node.  The special
  # values in this config will be automatically populated.
  cni_network_config: |-
    {
        "name": "calico",
        "type": "flannel",
        "delegate": {
          "type": "calico",
          "etcd_endpoints": "__ETCD_ENDPOINTS__",
          "etcd_key_file": "__ETCD_KEY_FILE__",
          "etcd_cert_file": "__ETCD_CERT_FILE__",
          "etcd_ca_cert_file": "__ETCD_CA_CERT_FILE__",
          "log_level": "info",
          "policy": {
              "type": "k8s",
              "k8s_api_root": "https://__KUBERNETES_SERVICE_HOST__:__KUBERNETES_SERVICE_PORT__",
              "k8s_auth_token": "__SERVICEACCOUNT_TOKEN__"
          },
          "kubernetes": {
              "kubeconfig": "/etc/kubernetes/cni/net.d/__KUBECONFIG_FILENAME__"
          }
        }
    }
  # If you're using TLS enabled etcd uncomment the following.
  # You must also populate the Secret below with these files.
  etcd_ca: "/calico-secrets/etcd-ca"
  etcd_cert: "/calico-secrets/etcd-cert"
  etcd_key: "/calico-secrets/etcd-key"
---

# The following contains k8s Secrets for use with a TLS enabled etcd cluster.
# For information on populating Secrets, see http://kubernetes.io/docs/user-guide/secrets/
apiVersion: v1
kind: Secret
type: Opaque
metadata:
  name: calico-etcd-secrets
  namespace: kube-system
data:
  # Populate the following files with etcd TLS configuration if desired, but leave blank if
  # not using TLS for etcd.
  # This self-hosted install expects three files with the following names.  The values
  # should be base64 encoded strings of the entire contents of each file.
  etcd-key: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFcEFJQkFBS0NBUUVBemR3MHQ1dDQ0bW9FLzdCZ0h3OHd3MkdBRURPa2ZzcTF1dTAwQWZ3Mzg2dzlPWnZqCit6WEg4Y0lTZjdXem9lcFE0OWNaWUEwT29tQ2IwRlJIcmVQeWMxQXZqSTBxQlZhUXNFOEs1MStVSWFXM0pDOTkKQk9VMGE1WXJzc25yWmFEN3NHdnh2aVp1c3FqUEtkMEFYVEw5ZGZZQVEvcXJ6Wk90eDVwNkNNRkE1a1dyU0k0bwp5aElaRklSZFRxWTlFam5yRWVrUWd5ZXRoWFg2VFFEcjZydkFkaU1Tbml3bVYvNm1kWVBpU1g0VWExdjN6T0x2ClV1dGdUZG9nc2JSWE5rMHNKZ3dlSm5heWZFM0RqNGNHSHJuUFo1TmRKOU1tYXk2K3E5L0dPQy91bEhZZkx0bVIKWGxkSlp4T3pHOFI5Zm00endiUS9vMVN6ajV0TDNqSGt2YWdBUndJREFRQUJBb0lCQVFERUFObFV4RHY1MzVYNAoyVGI3KzV1ak82OFNQZ2x1QzVXeWIrem53dnlIRHNTUW1HUWVLRnZDVE5VT01iUGlBMWFYU0pOSmg4VWFSNyt6ClFMdXF6bkZtNTc4T0pNNFFuUlpRT0VJYWdEOXJOZnhGQU00WkhoNm1IU3BvSmFieWNhK1NqNFJ4VnVocFJSOVMKTGJFT0MrR1dNZTZZeXhCUFFUL2gzNnczcjhFYVBSM1NNa0w4bUtVd1dnSkJzSXIwOFZsNU80RXd2dEdiWXhmOQpHWTZpYlkxa3VJamQ4QTNVUVU1ZElnc1JnelJBOXVwVXdwRCtEeVNrNWdSSlZzZ2pjREljRG91OWxNSmIrTDFnClFuS0I1eklwUzl5N3VSamI4SW1VUDJSYytmNlZNMHAyRTRGWWZCOUt4Z3JsSldOZFBqZW5HUTdYVHZPQWJKNzUKNTBKZlJNMTVBb0dCQVA2bExxNCtPNEZMU3ovblpWY2o0OWRielNNb2x6RVZhenR4Z3VacVlZRXlUdENjWU1KdApLdnYvME9yalkzeU9DR0VwYjYwRTB4QW9xU2gzS1lFK21LQzZrNzhwQlJwSTBqZTNFMW9Rb1BzcVZsNnk0bkFtCm9ieDU1eVRxbUd2aThuN3prWlczcnhlRnBYdFpmeUd3SkdCNElPV2ZuZGFtOGFtYWxxK0FiK1QxQW9HQkFNNzAKbElBVjJ6aTRTM3JXZmpldnErRzlLM2hsSExxYnFwMkNxd2hVQWFYQWF3ZjdTTENPcUJYT3NBUUtkbHFGZHNKUwpET1V5aVh3K2lOMFduSGlYUVZIcm9CNC8vNmNHWndvMitBMTNhclI1Z1h1djZwRmx0T2FCNXlKTDVWODk1Z0lqCnFxVTZxVnhrZUtUWjQzdnprUXdqN2xram53NWZhYW43cTNnN29HckxBb0dBUFRaajlFck5ieFUwYjVZbVRENDYKWk9pU2RXTXFoUWdMRU8wR2RIRWFJejUrcDdNUHAyMHFxZzBpb1hRcjFDTW9iSUxUYjVuN1NxVWJsTGlnWjRzdQprV1NJWTZsdXFwVGF3NzNranYvMFVxd291dmJ4YTlnOWhOc2c3MFRkZ3dHNUhUOWJ2Y3U3OFVHVk9BT1poZldiCnFQejNtM3MxTEFaUmpWMjEzMVpXajRrQ2dZQVhkbmNYaG1MU0xXS3hUSHdHeUMzT2ErbnZ2bGJoZVNDQXR2MjQKOWYweDAzek83Tmd5M0JtR1QyWjVMczU0T01WY0JuMWJ4cno3andOcFBWVUQvdkh5M2FuTGhDbnh2N0lTNEdTQwo4bFR3blEzbS9leTBDem5WSUVrYUJFeSszWCtrSW5NSVJIeEs4MEVTUDNtZzVkNDk2Qnp2a2pCSUt0MEozblIwCkx4NWl2UUtCZ1FESm5wc0hiby9vZkRqYllOSXBFV0RGK2N1clRPakJxTmwyZlp1R1VZNWZST3B5c1lsQnhkdS8KNlJEQXBCSDZyYWw4NjBVUjMwSDN0eUtaRWlYQU5xYjhYREE1eGQvV3g5YVBxUWJtSldOWDdpSEZ1bC9KOTJHQwpQeS8vR1hBeXIxZ2R1ZytFQXZlNm1scTlGT2RiN0YvSU5LMmlDcTlsZGkzQWJSL3N6QTJsVVE9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
  etcd-cert: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURqVENDQW5XZ0F3SUJBZ0lVTmcwYlU4M3RlWEdhejdqWFd4R0hJT1M3eGtvd0RRWUpLb1pJaHZjTkFRRUwKQlFBd2FqRUxNQWtHQTFVRUJoTUNWVk14RkRBU0JnTlZCQWdUQzB4dmN5QkJibWRsYkdWek1Rc3dDUVlEVlFRSApFd0pEUVRFVU1CSUdBMVVFQ2hNTFRrVlhSVWRISUVsT1F5NHhEREFLQmdOVkJBc1RBMFV4TVRFVU1CSUdBMVVFCkF4TUxUbVYzWldkbklFVlVRMFF3SGhjTk1UY3dOakl3TVRJd09EQXdXaGNOTWpjd05qRTRNVEl3T0RBd1dqQVIKTVE4d0RRWURWUVFERXdaamJHbGxiblF3Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLQW9JQgpBUUROM0RTM20zamlhZ1Qvc0dBZkR6RERZWUFRTTZSK3lyVzY3VFFCL0RmenJEMDVtK1A3TmNmeHdoSi90Yk9oCjZsRGoxeGxnRFE2aVlKdlFWRWV0NC9KelVDK01qU29GVnBDd1R3cm5YNVFocGJja0wzMEU1VFJybGl1eXlldGwKb1B1d2EvRytKbTZ5cU04cDNRQmRNdjExOWdCRCtxdk5rNjNIbW5vSXdVRG1SYXRJamlqS0Voa1VoRjFPcGowUwpPZXNSNlJDREo2MkZkZnBOQU92cXU4QjJJeEtlTENaWC9xWjFnK0pKZmhSclcvZk00dTlTNjJCTjJpQ3h0RmMyClRTd21EQjRtZHJKOFRjT1Bod1lldWM5bmsxMG4weVpyTHI2cjM4WTRMKzZVZGg4dTJaRmVWMGxuRTdNYnhIMSsKYmpQQnREK2pWTE9QbTB2ZU1lUzlxQUJIQWdNQkFBR2pnWU13Z1lBd0RnWURWUjBQQVFIL0JBUURBZ1dnTUJNRwpBMVVkSlFRTU1Bb0dDQ3NHQVFVRkJ3TUNNQXdHQTFVZEV3RUIvd1FDTUFBd0hRWURWUjBPQkJZRUZPZ0hwUnhzCllieUYvbzhrNEIwaGFObGJHZVFYTUI4R0ExVWRJd1FZTUJhQUZPLy92K0RTWHMvZXc5cFM3dHdTaEFKdnNTVkIKTUFzR0ExVWRFUVFFTUFLQ0FEQU5CZ2txaGtpRzl3MEJBUXNGQUFPQ0FRRUFjK0t6a0tNT29UekIxdjMvVXJDdApaN1kwUU42OHN3Tjh2TmVDeFMvajVLQ0llSlZvZ0dwVzR3ZmYvQjA0bXhRTUw2RFZlWi96bjFNelNVZEJZMU5QCko4UWF4SzREYndqSnpMMHk2blBDQXNXMG40NGoyKys2Y2NMdzIwUFA4Z0xHeXVhM1EyM2IwVElyVVYwNTEwcHYKbDkrSVZoaU52Y3RoT0JxQjd1cWFVU2pxcXdRUmJnbHBBeW4yYytSNGVGRXJhTXBMMFRYQkVmYXZXWE1SRG02QgphZGZVQ2wyamgxZU1wY2hRVmlHNHdoTGxYT2RDbkVTQTNzVUdWbnRXZmk5MG05QUVEa3RpUktmQVJzRjRhOW5HCkF6YndjaVVBbjRYT2pYT1oyNHlqM2lHUy96QzlzbVZXL2N4MkpjbVVXT3A1M1FwNjNIcU9OeTAweU5nL0ptR1UKemc9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
  etcd-ca: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUR5RENDQXJDZ0F3SUJBZ0lVR3dGczVkb1BkcXBRbjZsRTF0dUN3OCtJc0s4d0RRWUpLb1pJaHZjTkFRRUwKQlFBd2FqRUxNQWtHQTFVRUJoTUNWVk14RkRBU0JnTlZCQWdUQzB4dmN5QkJibWRsYkdWek1Rc3dDUVlEVlFRSApFd0pEUVRFVU1CSUdBMVVFQ2hNTFRrVlhSVWRISUVsT1F5NHhEREFLQmdOVkJBc1RBMFV4TVRFVU1CSUdBMVVFCkF4TUxUbVYzWldkbklFVlVRMFF3SGhjTk1UY3dOakl3TVRJd05EQXdXaGNOTWpJd05qRTVNVEl3TkRBd1dqQnEKTVFzd0NRWURWUVFHRXdKVlV6RVVNQklHQTFVRUNCTUxURzl6SUVGdVoyVnNaWE14Q3pBSkJnTlZCQWNUQWtOQgpNUlF3RWdZRFZRUUtFd3RPUlZkRlIwY2dTVTVETGpFTU1Bb0dBMVVFQ3hNRFJURXhNUlF3RWdZRFZRUURFd3RPClpYZGxaMmNnUlZSRFJEQ0NBU0l3RFFZSktvWklodmNOQVFFQkJRQURnZ0VQQURDQ0FRb0NnZ0VCQUtDczFmSGEKWnVkaWx3aEU3SGpoTE5GdHk0WEtZN3hmbWJoeTlxeEdzdnAvMzBEa3dUOFdoZTZtSytCcEp4SlJoVWcveWRSbwpRSWsyNWd1SHNFSjlRS2k4SEh0c1BFbXh1R0FkZXk0ajJVSWUzcFpJWWx6dGpNSFRPR2w4WTBTZDdDc1JwSVU3CllDeXUyejYxaEdMcks4c2xIRkJ3OXpDK2hnSDQ1eWV1LzhwYUJLbm1RREc0RjA4V1NReTEwYVBldy95NFBvcm4KWU4rbmh4UjFpSThjTGM5dFZDSEtQQk9EakRlQkxNZzBmNnBpaHF5ZjFXZ1V4eFZwY25Xa0hJVlpzZ1lMMHZLZAo4SC9VcG9BUnlvZU9sZCtHRlRHZ3VFc1F4bDR1aUR6V2V3aWxiTWg5LzVHd2RSbGVJOHZDdHd5R21VSkdxN3VKCnlBWVR5UEphT2pMNXFRTUNBd0VBQWFObU1HUXdEZ1lEVlIwUEFRSC9CQVFEQWdFR01CSUdBMVVkRXdFQi93UUkKTUFZQkFmOENBUUl3SFFZRFZSME9CQllFRk8vL3YrRFNYcy9ldzlwUzd0d1NoQUp2c1NWQk1COEdBMVVkSXdRWQpNQmFBRk8vL3YrRFNYcy9ldzlwUzd0d1NoQUp2c1NWQk1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQ0U0Rkl1CmxRNDZTZXhyWEtCVkcrNGRYcUpHK1cvVFUxeEJjKzNTeEdlQmFLc0xRVmRjY0FOckZtY0U3Sm5Sb0FzZXk2S0oKZXRVc0N6OGZaSHhESzFEaHJ6Tm9EdU0wcnppUEdnNmpIclBXdUVJRHNQTW0rNzUxNXZIc0pHN1NDNzdmdGZCWAplZ1pGdHNPZyswVVdOcGJ4YlhyQXR1V0dCTyt5cDZMcjZEL2VGa3VtVTRQQmQ3S2pvNmJLVEtsek5handKcGI0CkxXZjZoSEJqYm5QNDRqMk9DeWRMYWJZMkcvSFN6ZzJZVlVtTllPZ2JneG0wM2RXbWpDTHFDNVRPYndLVHlsdUoKUDUvUDZSZDM2VnVIMllaRHhhdEtvWGlYaGp3aUwxbVR4NytPS0RjRXVzRERKZUtnZ2ZRSmQzbFRQcXdrVmFXegpIb2UxVzlSdDFHZFhpcG45Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
---

# This manifest installs the calico/node container, as well
# as the Calico CNI plugins and network config on 
# each master and worker node in a Kubernetes cluster.
kind: DaemonSet
apiVersion: extensions/v1beta1
metadata:
  name: calico-node
  namespace: kube-system
  labels:
    k8s-app: calico-node
spec:
  selector:
    matchLabels:
      k8s-app: calico-node
  template:
    metadata:
      labels:
        k8s-app: calico-node
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ''
        scheduler.alpha.kubernetes.io/tolerations: |
          [{"key": "dedicated", "value": "master", "effect": "NoSchedule" },
           {"key":"CriticalAddonsOnly", "operator":"Exists"}]
    spec:
      hostNetwork: true
      containers:
        # Runs calico/node container on each Kubernetes node.  This 
        # container programs network policy and routes on each
        # host.
        - name: calico-node
          image: quay.io/calico/node:v1.1.1
          env:
            - name: FELIX_DEFAULTENDPOINTTOHOSTACTION
              value: "ACCEPT"
            # The location of the Calico etcd cluster.
            - name: ETCD_ENDPOINTS
              valueFrom:
                configMapKeyRef:
                  name: calico-config
                  key: etcd_endpoints
            # Choose the backend to use. 
            - name: CALICO_NETWORKING_BACKEND
              value: "none"
            # Disable file logging so `kubectl logs` works.
            - name: CALICO_DISABLE_FILE_LOGGING
              value: "true"
            - name: NO_DEFAULT_POOLS
              value: "true"
            # Location of the CA certificate for etcd.
            - name: ETCD_CA_CERT_FILE
              valueFrom:
                configMapKeyRef:
                  name: calico-config
                  key: etcd_ca
            # Location of the client key for etcd.
            - name: ETCD_KEY_FILE
              valueFrom:
                configMapKeyRef:
                  name: calico-config
                  key: etcd_key
            # Location of the client certificate for etcd.
            - name: ETCD_CERT_FILE
              valueFrom:
                configMapKeyRef:
                  name: calico-config
                  key: etcd_cert
          securityContext:
            privileged: true
          volumeMounts:
            - mountPath: /lib/modules
              name: lib-modules
              readOnly: false
            - mountPath: /var/run/calico
              name: var-run-calico
              readOnly: false
            - mountPath: /etc/resolv.conf
              name: dns
              readOnly: true
            - mountPath: /calico-secrets
              name: etcd-certs
        # This container installs the Calico CNI binaries
        # and CNI network config file on each node.
        - name: install-cni
          image: quay.io/calico/cni:v1.6.1
          imagePullPolicy: Always
          command: ["/install-cni.sh"]
          env:
            # CNI configuration filename
            - name: CNI_CONF_NAME
              value: "10-calico.conf"
            # CNI NET DIR
            - name: CNI_NET_DIR
              value: "/etc/kubernetes/cni/net.d"
            # The location of the Calico etcd cluster.
            - name: ETCD_ENDPOINTS
              valueFrom:
                configMapKeyRef:
                  name: calico-config
                  key: etcd_endpoints
            # The CNI network config to install on each node.
            - name: CNI_NETWORK_CONFIG
              valueFrom:
                configMapKeyRef:
                  name: calico-config
                  key: cni_network_config
            - name: ETCD_CA_CERT_FILE
              valueFrom:
                configMapKeyRef:
                  name: calico-config
                  key: etcd_ca
            # Location of the client key for etcd.
            - name: ETCD_KEY_FILE
              valueFrom:
                configMapKeyRef:
                  name: calico-config
                  key: etcd_key
            # Location of the client certificate for etcd.
            - name: ETCD_CERT_FILE
              valueFrom:
                configMapKeyRef:
                  name: calico-config
                  key: etcd_cert
          volumeMounts:
            - mountPath: /host/opt/cni/bin
              name: cni-bin-dir
            - mountPath: /host/etc/cni/net.d
              name: cni-net-dir
            - mountPath: /calico-secrets
              name: etcd-certs
      volumes:
        # Used by calico/node.
        - name: lib-modules
          hostPath:
            path: /lib/modules
        - name: var-run-calico
          hostPath:
            path: /var/run/calico
        # Used to install CNI.
        - name: cni-bin-dir
          hostPath:
            path: /opt/cni/bin
        - name: cni-net-dir
          hostPath:
            path: /etc/kubernetes/cni/net.d
        - name: dns
          hostPath:
            path: /etc/resolv.conf
        # Mount in the etcd TLS secrets.
        - name: etcd-certs
          secret:
            secretName: calico-etcd-secrets
        

---

# This manifest deploys the Calico policy controller on Kubernetes.
# See https://github.com/projectcalico/k8s-policy
apiVersion: extensions/v1beta1
kind: ReplicaSet 
metadata:
  name: calico-policy-controller
  namespace: kube-system
  labels:
    k8s-app: calico-policy
spec:
  # The policy controller can only have a single active instance.
  replicas: 1
  template:
    metadata:
      name: calico-policy-controller
      namespace: kube-system
      labels:
        k8s-app: calico-policy
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ''
        scheduler.alpha.kubernetes.io/tolerations: |
          [{"key": "dedicated", "value": "master", "effect": "NoSchedule" },
           {"key":"CriticalAddonsOnly", "operator":"Exists"}]
    spec:
      # The policy controller must run in the host network namespace so that
      # it isn't governed by policy that would prevent it from working.
      hostNetwork: true
      containers:
        - name: calico-policy-controller
          image: calico/kube-policy-controller:v0.5.4
          env:
            # The location of the Calico etcd cluster.
            - name: ETCD_ENDPOINTS
              valueFrom:
                configMapKeyRef:
                  name: calico-config
                  key: etcd_endpoints
            # Location of the CA certificate for etcd.
            - name: ETCD_CA_CERT_FILE
              valueFrom:
                configMapKeyRef:
                  name: calico-config
                  key: etcd_ca
            # Location of the client key for etcd.
            - name: ETCD_KEY_FILE
              valueFrom:
                configMapKeyRef:
                  name: calico-config
                  key: etcd_key
            # Location of the client certificate for etcd.
            - name: ETCD_CERT_FILE
              valueFrom:
                configMapKeyRef:
                  name: calico-config
                  key: etcd_cert
            # The location of the Kubernetes API.  Use the default Kubernetes
            # service for API access.
            - name: K8S_API
              value: "https://kubernetes.default:443"
            # Since we're running in the host namespace and might not have KubeDNS 
            # access, configure the container's /etc/hosts to resolve
            # kubernetes.default to the correct service clusterIP.
            - name: CONFIGURE_ETC_HOSTS
              value: "true"
          volumeMounts:
            # Mount in the etcd TLS secrets.
            - mountPath: /calico-secrets
              name: etcd-certs
      volumes:
        # Mount in the etcd TLS secrets.
        - name: etcd-certs
          secret:
            secretName: calico-etcd-secrets
```

> Replacing the `etcd-key:` `etcd-cert:` `etcd-ca:` with your own value in the `calico-etcd-secrets`, be very careful.

## Step 3: Install Calico

```
kubectl create -f calico.yaml
```

```
[root@e11k8sctl01 calico]# kubectl create -f calico.yaml 
configmap "calico-config" created
secret "calico-etcd-secrets" created
daemonset "calico-node" created
replicaset "calico-policy-controller" created
```

## Step 4: Check

```
kubectl get pod -n kube-system | grep calico
```

And the result would be like 

```
[root@e11k8sctl01 calico]# kubectl get pod -n kube-system -o wide | grep calico
calico-node-052fr                                           2/2       Running   4          38d       172.16.164.130   172.16.164.130
calico-node-23h7q                                           2/2       Running   2          38d       172.16.164.145   172.16.164.145
calico-node-327pm                                           2/2       Running   2          40d       172.16.164.121   172.16.164.121
calico-node-3494p                                           2/2       Running   4          38d       172.16.164.127   172.16.164.127
calico-node-3k5nt                                           2/2       Running   4          40d       172.16.164.125   172.16.164.125
calico-node-59xdl                                           2/2       Running   2          40d       172.16.164.124   172.16.164.124
calico-node-7p0gn                                           2/2       Running   4          40d       172.16.164.111   172.16.164.111
calico-node-87zlb                                           2/2       Running   32         38d       172.16.164.147   172.16.164.147
calico-node-99rxd                                           2/2       Running   4          38d       172.16.164.133   172.16.164.133
calico-node-bnz5b                                           2/2       Running   4          38d       172.16.164.134   172.16.164.134
calico-node-cp82r                                           2/2       Running   4          38d       172.16.164.136   172.16.164.136
calico-node-d2r9n                                           2/2       Running   2          38d       172.16.164.141   172.16.164.141
calico-node-d77xt                                           2/2       Running   4          38d       172.16.164.135   172.16.164.135
calico-node-jdm1d                                           2/2       Running   2          38d       172.16.164.144   172.16.164.144
calico-node-jlltl                                           2/2       Running   2          38d       172.16.164.143   172.16.164.143
calico-node-kn8ft                                           2/2       Running   4          38d       172.16.164.137   172.16.164.137
calico-node-mdw34                                           2/2       Running   4          38d       172.16.164.128   172.16.164.128
calico-node-mnpnk                                           2/2       Running   4          38d       172.16.164.139   172.16.164.139
calico-node-mrz11                                           2/2       Running   4          38d       172.16.164.138   172.16.164.138
calico-node-mxw8k                                           2/2       Running   2          40d       172.16.164.122   172.16.164.122
calico-node-q1rx9                                           2/2       Running   4          40d       172.16.164.112   172.16.164.112
calico-node-q8mmc                                           2/2       Running   4          38d       172.16.164.140   172.16.164.140
calico-node-qgr46                                           2/2       Running   2          40d       172.16.164.123   172.16.164.123
calico-node-skt17                                           2/2       Running   2          38d       172.16.164.142   172.16.164.142
calico-node-slxc0                                           2/2       Running   8          40d       172.16.164.113   172.16.164.113
calico-node-twlc9                                           2/2       Running   4          38d       172.16.164.126   172.16.164.126
calico-node-v1vlm                                           2/2       Running   4          38d       172.16.164.129   172.16.164.129
calico-node-vgv7h                                           2/2       Running   4          38d       172.16.164.131   172.16.164.131
calico-node-x2czd                                           2/2       Running   4          38d       172.16.164.132   172.16.164.132
calico-node-z6j84                                           2/2       Running   2          38d       172.16.164.146   172.16.164.146
calico-policy-controller-qktx0                              1/1       Running   0          5d        172.16.164.143   172.16.164.143

```