---
title: K8S - Application Lifecycle Management
date: 2022-05-01 19:31:52
tags: K8S
---

# Strategy
```yaml
# Deployment
spec:
  strategy:
    type: Recreate/RollingUpdate
    # type=RollingUpdate
    rollingUpdate:
      maxUnavailable: 25%
      maxSurge: 25%
```

# Rollout
```sh
# Current status
kubectl rollout status deploy/my-deploy
# History
kubectl rollout history ...
# Rollback
kubectl rollout undo ...
```
```sh
# Easily set image
kubectl set image deploy/my-deploy nginx=nginx:1.9.1
```

# Command & Arguments
- Ex. the sleeper image
```dockerfile
FROM Ubuntu
ENTRYPOINT [ "sleep" ]
CMD [ "5" ]
```
- pass arguments
```sh
# For docker command
docker run --name sleeper sleeper 10
```
```yaml
# For kubernetes
# Ex. Pod
spec:
  containers:
  - args: [ "10" ]
```
- Overwrite entrypoint
```sh
# For docker command
docker run --name sleeper sleeper --entrypoint sleep 10
```
```yaml
# For kubernetes
# Ex. Pod
spec:
  containers:
  - command: [ "sleep" ]
    args: [ "10" ]
```

# Environment Variables
```yaml
# Ex. Pod
spec:
  containers:
  - env:
    - name: name
      value: value
```

# ConfigMaps
```sh
# Create
kubectl create configMap my-cm --from-literal=key=value
kubectl create configMap my-cm --from-file=path
```
```yaml
# Create
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config-map
data:
  key1: value1
  key2: value2
```
```yaml
# Usage: Ex. Pod
spec:
  containers:
  - env:
    - name: key1
      valueFrom:
        configMapKeyRef:
          name: my-config-map
          key: key1
    # Or
    envFrom:
    - configMapRef:
        name: my-config-map
  # Or
  volumes:
  - name: my-volume
    configMap:
      name: my-config-map
```

# Secret
```sh
# Create
kubectl create secret generic my-secret --from-literal=key=value
kubectl create secret generic my-secret --from-file=path
```
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
data:
  key1: base64-value1
  key2: base64-value2
```
```yaml
# Usage: Ex. Pod
spec:
  containers:
  - env:
    - name: key1
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: key1
    # Or
    envFrom:
    - secretRef:
        name: my-secret
  # Or
  volumes:
  - name: my-volume
    secret:
      secretName: my-config-map
```

# Init Containers
- Containers that only run at init phase
```yaml
# Ex. Pod
spec:
  initContainers:
  - name: xxx
    image: xxx
  containers:
  - name: xxx
    image: xxx
```
