---
title: DVA - ELB
date: 2022-02-19 20:00:27
tags: AWS - Developer
---

# CLB

- Fixed hostname

# ALB

- Support HTTP(S), HTTP/2, WebSocket
- Support redirects (Ex. from HTTP to HTTPS)
- Support routing to different target groups based on path, hostname, query string, headers
- Supported target groups:
  - EC2 instances
  - ECS tasks
  - Lambda functions
  - Private IP addresses
- Fixed hostname
- Client info in headers
  - IP: X-Forwarded-For
  - Port: X-Forwarded-Port
  - Protocol: X-Forwarded-Proto

# NLB

- Supported target groups:
  - EC2 instances
  - Private IP addresses
  - ALB
- One static IP per AZ (fixed IPs)

# Gateway Load Balancer

- Layer 3 (Network Layer) - IP Packets load balancer
- Combines:
  - Transparent Network Gateway
  - Load Balancer
- Uses GENEVE protocol on port 6081
- Target groups
  - EC2 instances
  - Private IP addresses
