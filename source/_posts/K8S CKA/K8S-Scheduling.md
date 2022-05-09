---
title: K8S - Scheduling
date: 2022-04-30 15:27:08
tags: K8S
---

# Manual Scheduling
- Set node (modify nodeName will not work)
```yaml
# Ex. Pod
spec:
  nodeName: node01
```
- Create a Binding object instead, add pass the binding to api in `data` parameter

# Label Selector
```yaml
kubectl get pods --selector app=my-app
```
- Use annotations to store non-selector metadata

# Taints and Tolerations
- Taints mark nodes
```sh
# add
kubectl taint nodes node-name key=value:taint-effect
# remove
kubectl taint nodes node-name key=value:taint-effect-
```
- taint-effect: NoSchedule, PreferNoSchedule, NoExecute
- Tolerations mark pods
```yaml
# Ex. Pod
spec:
  tolerations:
  - key: key
    operator: Equal
    value: value
    effect: taint-effect
```

# Node Selectors
- Label nodes
```sh
kubectl label nodes node-name key=value
```
- Configure node selector
```yaml
# Ex. Pod
spec:
  nodeSelector:
    key: value
```

# Node Affinity
```yaml
# Ex. pod
spec:
  affinity:
    nodeAffinity:
      <type>:
        nodeSelectorTerms:
        - matchExpressions:
          - key: key
            operator: In/NotIn/Exists/etc.
            values: values
```
- Types
  - requireDuringSchedulingIgnoreDuringExecution
  - preferredDuringSchedulingIgnoredDuringExecution
  - requiredDuringSchedulingRequiredDuringExecution

# Resource Requirements and Limits
```yaml
# Ex. Pod
spec:
  containers:
    resources:
      requests:
        memory: 256Mi
        cpu: 0.5
      limits:
        memory: 512Mi
        cpu: 1
```
- Can create `LimitRange` object to overwrite default requests and limits
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: new-limit-range
spec:
  limits:
  - default:
      memory: 1Gi
      cpu: 2
    defaultRequest:
      memory: 512Gi
      cpu: 1
    type: Container
```

# Daemon Sets
- Make sure that a specify pod always runs on each node
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: my-daemon-set
spec:
  selector:
    matchLabels:
      app: my-daemon
  template:
    metadata:
      labels:
        app: my-daemon
    spec:
      containers:
      - name: daemon-name
        image: daemon-image
```

# Static Pods
- Without master node, worker node can also create pods based on pod definition in the manifest directory
- manifest directory path can be configured in kubelet.service
```sh
ExecStart= ... \\
  --pod-manifest-path=/etc/Kubernetes/manifests \\
  # or
  --config=kubeconfig.yaml \\
  ...
```
```yaml
# kubeconfig.yaml
staticPodPath: /etc/Kubernetes/manifests
```
- Use `docker ps` command to confirm pods if there is not kube-api-server
- Or use `kubectl get pods` to get both pods and static pods. But you cannot use kubectl to modify static pods

# Multiple Schedulers
- Set default scheduler
```sh
# kube-scheduler.service
ExecStart= ... \\
  --scheduler-name=custom-scheduler \\
  ...
```
- Create a new scheduler
```yaml
# Copy from kube-scheduler
# add
spec:
  containers:
  - command:
    - --scheduler-name=custom-scheduler
    - --lock-object-name=custom-scheduler
```
- Use scheduler
```yaml
# Ex. Pod
spec:
  containers:
  - schedulerName: customer-scheduler
```
