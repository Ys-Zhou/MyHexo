---
title: 'Summary: Storage'
date: 2020-07-31 15:33:26
tags: 'AWS - Solutions Architect'
---

# EBS & EC2 Instance Store

## EBS Volume Types

- gp2
  - General Purpose SSD
  - 3 IOPS / GB 
    - min 100 IOPS
    - burst to 3000 IOPS
    - max 16000 IOPS
  - 1 GB - 16 TB
- io1
  - IOPS Optimized SSD
  - Provisioned IOPS
    - min 100 IOPS
    - max 64000 IOPS for Nitro instances, 32000 IOPS for others
  - 4 GB - 16 TB
- st1
  - Throughput Optimized HDD
  - 500 MB / s
  - 500 GB - 16 TB
- sc1
  - Cold HDD
  - 250 MB / s
  - 250 GB - 16 TB

## EBS Snapshots

- Use incremental snapshots
- Snapshots will be stored in S3
- AWS recommend to detach volume to do snapshot but not necessary
- Can copy snapshots across region
- Can create AMI from snapshots
- You need to read the snapshot (use *fio* or *dd* command) before restore volume
- Snapshots can be automated using Amazon Data Lifecycle Manager

## EC2 Instance Store

- Physical disk attached to the EC2 physical server
- Very high IOPS
- Cannot resize
- Ephemeral (on stop or termination)

# EFS

- Only compatible with Linux instances
- Use security group to control access to EFS
- Encryption at rest using KMS
- Can only attach to one VPC, create one ENI per AZ
- 1000s of concurrent NFS clients, over 10 GB / s throughput

## EFS Settings

- Performance Mode
  - General purpose (default)
  - Max I/O (higher latency, higher throughput)
- Throughput Mode
  - Bursting mode (more FS size more burst throughput)
  - Provisioned IO mode (expensive)
- Storage Tiers
  - Standard
  - Infrequent access (higher cost to retrieve files, lower to store files)

## EFS Sharing

- From VPC
  - VPC peering
- From On-Premise
  - Direct Connect
  - VPN
