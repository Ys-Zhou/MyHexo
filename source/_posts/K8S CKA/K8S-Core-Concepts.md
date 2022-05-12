---
title: K8S - Core Concepts
date: 2022-04-29 12:09:23
tags: K8S
---

# ETCD
- A distributed key-value store
- Is a service that listens on 2379 by default

# ETCD in Kubernetes
- Kubernetes stores information in the ETCD server
  - Update information in the ETCD server is the last action when you make any update
- Deployed from **Scratch**
  - ETCD will be a independent service on the host
- Deploy from **Kubeadm** tool
  - ETCD will be a pod called `etcd-master` under the `kube-system` namespace
```sh
# Get pod's shell
kubectl exec etcd-master -n kube-system
# Get all keys
etdectl get / --prefix -key
```
- ETCD in HA Environment
  - set `controller-{i}=https://{CONTROLLER_IP}:2380` to `initial-cluster` parameter

# Kube-Apiserver
- Authenticating and validating requests, retrieving and updating data in ETCD
- The other components such as the scheduler, kube-controller-manager, kubelet use kube-apiserver to perform updates in the cluster in their respective areas
- Configurations
  - **Kubeadm** pod: kube-apiserver-master
  - **Kubeadm** config: */etc/kubernetes/manifests/kube-apiserver.yaml*
  - Check config: ps -aux | grep kube-apiserver

# Kube-Controller-Manager
- Controller-Manager
  - A service contains all of the necessary controllers
- Node-Controller
  - Continually checks the status of nodes. When a node becomes unreachable, node-controller will eventually recreate the pods onto healthy nodes
- Replication-Controller
  - Continually monitors the replica sets, and ensures that the desired number of pods are available
- Configurations
  - **Kubeadm** pod: kube-controller-manager-master
  - **Kubeadm** config: /etc/kubernetes/manifests/kube-apiserver.yaml
  - **Scratch** config: /etc/systemd/system/kube-controller-manager.service
  - Check config: ps -aux | grep kube-controller-manager

# Kube-Scheduler
- Assign the best node to create a pod to satisfy the requirement (CPU, memory , etc.)
- Judgement processing
  - Filter nodes
  - Rank nodes
- Configurations
  - **Kubeadm** pod: kube-scheduler
  - **Kubeadm** config: /etc/kubernetes/manifests/kube-scheduler.yaml
  - Check config: ps -aux | grep kube-scheduler

# Kubelet
- At worker nodes
  - Register node
  - Create pods
  - Monitor node & pods
- Configurations
  - You must manually install it onto worker nodes
  - Check config: ps -aux | grep kubelet

# Kube-Proxy
- A process that runs on each node
- When a new service is created, kube-proxy will create the appropriate rules to forward traffic to those services to the backend pod
- Configurations
  - **Kubeadm** pods: kube-proxy-xxxxx (as daemonset)

# Recap

## Pods (po)
```sh
# Generate yaml from command
kubectl run nginx --image=nginx --dry-run=client -o yaml
# Get yaml from existing one
kubectl get po nginx -o yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: my-app
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

## ReplicaSets (rs)
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-replica-set
  labels:
    app: my-app-rs
spec:
  template:
    metadata:
      name: my-pod
      labels:
        app: my-app
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPost: 80
  replicas: 2
  selector:
    matchLabels:
      app: my-app
```

## Deployments (deploy)
```sh
# Generate yaml from command
kubectl create deploy nginx --image=nginx --replicas=4 --dry-run=client -o yaml
# Get yaml from existing one
kubectl get deploy nginx -o yaml
```
```yaml
# Similar with ReplicaSet
kind: Deployment # <- ReplicaSet
```

## Services (svc)
- NodePort, ClusterIP, LoadBalancer
- Service is Cross-node
```yaml
# NodePort
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  ports:
  - targetPort: 80
    port: 80
    nodePort: 30080
  selector:
    app: my-app
```
```yaml
# ClusterIP
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  ports:
  - targetPort: 80
    port: 80
  selector:
    app: my-app
```
## Namespaces (ns / -n)
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```
```sh
# Use namespace
-n=dev
--namespace=dev
# In all namespaces
--all-namespaces
# Set current namespace
kubectl config set-context $(kubectl config current-context) --namespace=dev
```
- Access a service in a different namespace: **{service}.{namespace}.svc.cluster.local**

## ResourceQuotas (quota)
- Set quota for a namespace
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-quota
  namespace: dev
spec:
  hard:
    pods: 10
    requests:
      memory: 2Gi
      cpu: 4
    limits:
      memory: 4Gi
      cpu: 8
```
