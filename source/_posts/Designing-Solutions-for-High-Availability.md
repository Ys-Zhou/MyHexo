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

## Disaster Recovery Features

### AMI for EC2

- Configure and identify your own AMIs
- You can copy your AIMs to other regions for disaster recovery

### Storage Gateway

- File gateway
  - Store your file in S3
- Volume gateway
  - Cached Mode and Stored Mode
  - Data on the volumes is stored in S3
- Tape gateway
  - Use Virtual Tape Library to backup your data to S3 or Glacier (Virtual Tape Shelf, VTS)

### AWS Import/Export

- Use Snowball to migrate your data into S3, Glacier, or EBS snapshots

### VM Import/Export

- Import virtual machine images from your existing environment to EC2 instances or vice versa
- No charge

### VPC

- Use VPC to extend your existing network
  - Through VPN
  - Direct Connect

### RDS

- RDS gives you the ability to snapshot data form one region to another
- RDS can run a read replica in another region

### DynamoDB

- DynamoDB allows you to copy data to DynamoDB in another region or to S3

### Redshift

- You can snapshot your Redshift to S3 within the same region or copy to another region 

### Other services

- S3
- EBS
- Route53
- ELB

## Disaster Recovery Approaches

- Cost (low -> high) & recovery speed (slow -> fast)
  - Backup & Restore
  - Pilot Light
    - A minimal version of an environment is always running in the cloud
  - Warm standby
  - Multi Site

### Backup & Restore

- S3 & Glacier
  - An ideal destination for backup data
  - Transferring data to/from S3
    - Internet
    - Direct Connect
    - AWS Import/Export (Snowball)

- Storage Gateway
  - Volume Gateway
    - Stored mode (Gateway Stored)
      - Enables snapshots of your on-premises data volumes to be transparently copied into S3 for backup
    - Cached mode (Gateway Cached)
      - Allows you to store your data in S3, and keep your frequently accessed data local
  - Tape Gateway
    - Can be set as a backup target for your existing backup management software
    - Can be used as a replacement for traditional tape backup

- Key steps for backup and restore
  - Select an appropriate tool or method to back up your data into AWS
  - Ensure that your have an appropriate retention policy for the data
  - Ensure that appropriate security measures (encryption and access policies) are in place for the data
  - Regularly test the recovery of the data and the restoration of your system

### Pilot Light

- Only run the most critical core elements of your systems in AWS
  - You can rapidly provision a full-scale production environment around the critical core
  - The critical core elements typically include database servers, which would be replicated to EC2 or RDS

- Main options for provisioning for recovery
  - Use EIP
  - Use ELB

- Key steps for preparation
  - Set up EC2 instances to replicate
  - Ensure that all custom software packages are available in AWS
  - Create and maintain AMIs of key servers
  - Regularly run these servers, test them and apply updates
  - Consider automating the provisioning of AWS resources

- Key steps for recovery
  - Start your application EC2 instances from your custom AMIs
  - Resize database/data store instances according to the increased traffic
  - Add additional database/data store instances to ensure the resilience in the data tier
    - If using RDS, turn on Multi-AZ
  - Change DNS to point at the EC2 servers
    - Either EIP or ELB
  - Install and configure any non-AMI based systems, ideally in an automated way

### Warn Standby

- A scaled-down version of the fully functional environment is always running in the cloud
- These servers can be running on a minimum-sized fleet of EC2 instances on the smallest sizes

- Key steps for preparation
  - Set up EC2 instances to replicate
  - Create and maintain AMIs
  - Run your application using a minimal footprint of EC2 instances
  - Patch and update software and configuration files in line with your live environment

- Key steps for recovery
  - Increase the number of EC2 fleets in service with ELB (horizontal scaling)
  - Start applications on larger EC2 instance types (vertical scaling)
  - Either manually change the DNS, or use Route53 automated health checks so that all traffic is routed to the AWS environment
  - Consider using Auto Scaling to right-size the fleet or accommodate the increased load
  - Add resilience or scale up your database

### Multi Site

- A multi-size solution runs in AWS as well as on your existing on-site infrastructure, in an active-active configuration
- You can replicate your data in synchronous or asynchronous methods
- You can use Route53 to support weighted routing

- Key steps for preparation
  - Set up your AWS environment ro duplicate your production environment
  - Set up DNS such as Route53 to distribute incoming requests to both sites
  - Configure automated failover to re-route traffic away from the affected site

- Key steps for recovery
  - Either manually or by using DNS failover, change the DNS weighting so that all requests are sent to the AWS site
  - Have application logic for failover to use the AWS database servers for all queries
  - Consider using Auto Scaling to automatically right-size the AWS fleet

## Replication of Data

- Distance between the sites
- Available bandwidth
- Data rate required by your application
- Replication technology (like using parallel)

## Self Healing

- SQS to decouple
- CloudWatch and Auto Scaling to terminate unhealthy instances
  - Auto Scaling to create replacement instances to replace terminated instances
- S3 and Glacier performs regular, systematic data integrity checks and is built to be automatically self-healing
