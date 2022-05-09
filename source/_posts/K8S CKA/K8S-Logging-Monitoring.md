---
title: K8S - Logging & Monitoring
date: 2022-05-01 14:05:11
tags: K8S
---

# Metrics Server
- Collect and store metrics in-memory
```sh
# Install files
git clone https://github.com/kubernetes-incubator/metrics-server.git
# Check
kubectl top node/pod
```

# Logging
```sh
kubectl logs -f pod-name [container-name]
```
