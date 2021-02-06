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
- Endpoints Gateway can only be accessed within the VPC

## VPC Endpoints Interface

- Create a Endpoint Network Interface in a subnet with a hostname and a private IP
- Leverage Security Groups
- Enable Private DNS to resolve public hostname to endpoints

## VPC Endpoints Policies

- JSON documents to control access to services
- Does not replace IAM policies or service-specific policies, they works together

# AWS PrivateLink

- Connect to NLB through PrivateLink Endpoint using private network

# VPN

## Site-to-Site VPN (AWS Managed VPN)

- On-premise
  - Setup a software or hardware VPN
- On AWS
  - Setup a Virtual Private Gateway (VGW) attached to the VPC
  - Setup a Customer Gateway (CGW) pointed to the on-premise VPN
- VPN connections are redundant, encrypted using IPSec
- Can accelerate connections using Global Accelerator

### Routing in Site-to-Site VPN

- Static Routing
  - On-premise: pointing VPC private CIDR to CGW
  - VPC: pointing on-premise private CIDR to VGW
- Dynamic Routing
  - Uses BGP (Border Gateway Protocol) to share routes automatically
  - Need to specify the ASN (Autonomous System Number) of the CGW and VGW

### Internet Access through Site-to-Site VPN

- On-premise side
  - Access Internet through NAT Gateway in VPC
    - No: NAT Gateway only allow the sources in the VPC
  - Access Internet through NAT Instance in VPC
    - Yes
- VPC side
  - Access Internet through on-premise NAT
    - Yes

### VPN CloudHub

- Connects multiple CGW to a VGW
- All on-premise networks and the VPC can connect to each other
- Low cost hub-and-spoke model for network connectivity

## Software VPN

- You can setup your own software VPN managed by yourself

## AWS Client VPN

- Connect from personal computer to a private network in AWS or on-premise

## Solutions: VPN to multiple VPCs

- Solution 1: Create separate VPN connection for each VPC
  - You can connect multiple VGW to a CGW
- Solution 2: Use Direct Connect Gateway
- Solution 3: Create a shared VPC
  - Replicate or proxy your on-premise services to a shared VPC
  - Connect VPCs to the shared VPC using VPC peering
- Solution 4: Use transit software VPN
- Solution 5: Use Transit Gateway

# AWS Direct Connect

- Provides a dedicated private connection from a remote network to your VPC
- The connection is setup between your network and AWS Direct Connect locations
- Have private access to AWS services
- Bypass ISP: reduce network cost, increase bandwidth and stability
- Not redundant by default (setup another Direct Connect connection or failover VPN)

## Direct Connect Virtual Interfaces (VIF)

- Public VIF
  - Connect to public AWS Endpoints through Direct Connect (privately)
  - S3, etc.
- Private VIF
  - Connect to resources in your VPC
- You cannot access VPC Endpoints through Private VIF
  - Simply use Public VIF

## Connection Types

- Dedicated Connections
  - Dedicated physical ethernet port
  - Connections requests made to AWS first, then completed by Direc Connect Partners
- Hosted Connections
  - Connection requests are made via Direc Connect Partners
  - Capacity can be added or removed on demand
- Need longer than 1 month to establish a new connection

## Encryption

- Data in transit is not encrypted but is private
- You can create VPN connections between the AWS Direct Connect location and your data center

## Link Aggregation Groups (LAG)

- Get increased speed and failover by aggregating Direct Connect connections into a logical one
- Can aggregate up to 4 connections
- Can add connections over time to the LAG
- All connections in the LAG must have the same bandwidth
- All connections in the LAG must terminate at the same Direct Connect Endpoint

## Direct Connect Gateway

- Setup a Direct Connect to multiple VPCs in different regions (can cross account)
- Connect Private VIF to Direct Connect Gateway
- Can connect Direct Connect Gateway to Transit Gateway
