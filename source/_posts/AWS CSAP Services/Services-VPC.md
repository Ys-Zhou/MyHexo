---
title: 'Services: VPC'
date: 2020-08-23 17:53:48
tags: 'AWS - Solutions Architect (Services)'
---

# VPC Peering

- Two VPCs must not have overlapping CIDR
- You have update route tables in each subnets to ensure instance can communicate
- You can do VPC peering with another AWS account
- VPC Peering cannot cross regions
- VPC Peering is not transitive
  - Use VPN or Transit Gateway

# Transit Gateway

- Transit Gateway is a hub-and-spoke connection, networks connected to Transit Gateway can communicate with others
- Transit Gateway can share cross-region
- Transit Gateway can share cross-account using Resource Access Manager (RAM)
- Supports IP Multicast

# VPC Endpoints

- Connect to AWS services through private network
- VPC Endpoints Gateway (S3 and DynamoDB)
- VPC Endpoints Interface (others)

## VPC Endpoints Gateway

- One Gateway per VPC
- Must update route tables
- Enable Private DNS to resolve public hostname to endpoints

## VPC Endpoints Interface

- Create a Endpoint Network Interface in a subnet with a hostname and a private IP
- Leverage Security Groups
- Enable Private DNS to resolve public hostname to endpoints

## VPC Endpoints Policies

- JSON documents to control access to services
- Does not replace IAM policies or service-specific policies, they works together
