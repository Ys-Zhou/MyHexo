---
title: K8S - Security
date: 2022-05-04 16:18:17
tags: K8S
---

# Authentication

- Accounts
  - User
  - Service Accounts

# Auth Mechanisms

## Static Password File
user.csv
```csv
password1,userName1,userID1,group1
password2,userName2,userID2,group2
```
kube-apiserver.service
```sh
--basic-auth-file=user.csv
```
Authenticate
```sh
curl -v -k https://master-ip:6443/api/v1/pods -u "userName:password"
```

## Static Token File
user-token.csv
```csv
token1,userName1,userID1,group1
token2,userName2,userID2,group2
```
kube-apiserver.service
```sh
--token-auth-file=user-token.csv
```
Authenticate
```sh
curl -v -k https://master-ip:6443/api/v1/pods --header "Authorization: Bearer token1"
```

# TLS in Kubernetes

```sh
# Look into certificate
openssl x509 -in xxx.crt -text -noout
```
```sh
# Get logs without kube-apiserver
docker ps -a
docker logs container-id
```

# Certificate API
```sh
# Generate user certificate
openssl genrsa -out me.key 2048
openssl req -new -key me.key -subj "/CN=me" -out me.crt
```
```yaml
# Create certificate object
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: me
spec:
  usages:
  - digital signature
  - key encipherment
  - server auth
  signerName: example.com/me
  request: |
    ${me.crt | base64}
```
```sh
# Get & approve
kubectl get csr
kubectl certificate approve me
```

# KubeConfig
- Default file is ~/.kube/config
  - Clusters
  - Contexts
  - Users
```yaml
apiVersion: v1
kind: Config

current-context: me@my-cluster
clusters:
- name: my-cluster
  cluster:
    certificate-authority: ca.crt
    server: https://localhost:6443
contexts:
- name: me@my-cluster
  context:
    cluster: my-cluster
    user: me
    namespace: default
users:
- name: me
  user:
    client-certificate: me.crt
    client-key: me.key
```
```sh
# Usage
kubectl config view --kubeconfig=my-config
kubectl config use-context me@my-cluster
```

# Authorization
- Mechanisms (mode)
  - Node
  - ABAC (user/group based)
  - RBAC (role based)
  - Webhook
  - AlwaysAllow
  - AlwaysDeny

## RBAC
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: my-role
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "get", "create", "update", "delete"]
  resourceNames: ["name1", "name2"]
```
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: my-role-binding
subjects:
- kind: User
  name: me
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: my-role
  apiGroup: rbac.authorization.k8s.io
```

## Check Access
```sh
# kubectl auth can-i command -- as user
kubectl auth can-i create pod --as me
kubectl auth can-i delete pod --as me
```

## Cluster Roles
```yaml
# Similar with Role
kind: ClusterRole
```
```yaml
# Similar with RoleBinding
kind: ClusterRoleBinding
```
