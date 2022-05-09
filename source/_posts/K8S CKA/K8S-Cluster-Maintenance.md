---
title: K8S - Cluster Maintenance
date: 2022-05-02 16:36:34
tags: K8S
---

# Maintenance Nodes
```sh
# move pods to other nodes and cordon the node
kubectl drain node1
# mark node unschedulable
kubectl cordon node1
# mark node schedulable
kubectl uncordon node1
```

# Cluster Upgrade Process
- Compatible versions
  - kube-apiserver: v1.x
  - controller-manager, kube-scheduler: v1.(x-1) ~ v1.x
  - kubelet, kube-proxy: v1.(x-2) ~ v1.x
  - kubectl: v1.(x+1) ~ v1.(x-1)
- Supported versions
  - v1.(x-2) ~ v1.x (current)
  - You should upgrade by 1 minor version each time
- Kubeadm upgrade process
  - https://kubernetes.io/zh/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/

# Backup
- Resource Configuration
- ETCD Cluster
  - https://kubernetes.io/zh/docs/tasks/administer-cluster/configure-upgrade-etcd/#%E5%A4%87%E4%BB%BD-etcd-%E9%9B%86%E7%BE%A4
- Persistent Volumes
