---
title: Designing Solutions for High Availability
date: 2019-12-04 08:52:00
tags: AWS - Solutions Architect
---

# Building Fault Tolerance Applications

## AMI

- Being able to quickly launch replacement instances based on AMI is a critical first step towards fault tolerance
- AMI can able you to quickly recover from failures
  - If an instance fails, you can simply launch another one based on the same template
- To minimize downtime, your might event always keep a spare instance based on the same AMI
  - This can be down efficiently using EIP
  - You can easily replacement instance by remapping your EIP to the new instance

## EBS

- Use **snapshots** to backup EBS volumes
  - Snapshots can be used to create new EBS volumes at the time the snapshot was taken
  - Snapshots only represent the on-disk state of the application, you must pause I/O and flush in-memory data to disk before initiating a snapshot

- Fault tolerance of EBS
  - EBS volumes store data redundantly, making them more durable than a typical hard drive
  - A backup strategy includes an interval, a retention period, and a recovery plan
    - Interval: time between backups
    - Retention period: time to keep the snapshots
    - Recovery plan: procedures to restore the snapshots
  - Snapshots are stored in S3
  - EBS snapshots can be copied into other regions to create EBS volumes

## Auto Scaling

- Use Auto Scaling with CloudWatch to control termination and launching new EC2 instances
- Auto Scaling will automatically detect failures through health check and launch replacement instances
- It is recommended to using Auto Scaling to regularly turn your instances over
  - In order to avoid any possible memory leaks or degradation
  - Set expiry dates on your instances to ensure they remain fresh

## ELB

- Any requests sent to ELB DNS host name are delegated to a pool of EC2 instances
- ELB is bound to a region
- ELB detects unhealthy instances within its pool and automatically reroutes traffic to healthy instances, until the unhealthy have benn restored

## Regions and AZs

- Distributing your application geographically is a key element to achieving better fault tolerance
- Regions consist of several AZs, are geographically dispersed
- AZs are distinct locations in a Region
  - Provide low latency network connectivity to other AZs in the same Region
- EC2 SLA is 99.95% availability for each Region

## Multi-AZ Architecture

- Use redundant instances for each tier of an application
- Use ELB to reduce manual intervention
- Auto Scaling can work across multiple AZs in a Region

## Route53

- Can also use Route53 to build applications across Regions for higher availability

## S3

- S3 redundantly on multiple AZs in a Region
- S3 Versioning and deletion MFA protect against accidental deletions
- By using S3, you can delegate the responsibility of fault tolerance of data storage

## RDS

- Automated backups
  - Enable point-in-time recovery for your database instance
  - RDS will back up your database and **5-minutes-interval transaction logs** and store both for a user-specified retention period by default

- Manual Snapshots
  - You can create a new instances from a Snapshot
  - Help you to recover from higher-level faults such as unintentional data modification
  
- Multi-AZ deployment
  - Provision a synchronous standby replica of your database in a different AZ
  - In case of failover scenario, the standby will be promoted to be the primary
    - **Standby replica is different from read replica**

# Using AWS for Disaster Recovery

## RTO and RPO

- Recovery Time Objective (RTO)
  - The time it takes after a disruption to back to its service level
- Recovery Point Objective (RPO)
  - The acceptable amount of data loss measured in time

## AMI for EC2

- Configure and identify your own AMIs
- You can copy your AIMs to other regions for disaster recovery

## Storage Gateway

- File gateway
  - Store your file in S3
- Volume gateway
  - Cache Mode and Stored Mode
  - Data on the volumes is stored in S3
- Tape gateway
  - Use Virtual Tape Library to backup your data to S3 or Glacier (Virtual Tape Shelf, VTS)

## AWS Import/Export

- Use Snowball to migrate your data into S3, Glacier, or EBS snapshots

## VM Import/Export

- Import virtual machine images from your existing environment to EC2 instances or vice versa
- No charge
