---
title: 'Services: Migration'
date: 2020-08-16 20:10:52
tags: 'AWS - Solutions Architect (Services)'
---

# 6R of Cloud Migrations

## Rehosting

- Lift and shift
- Simple migrations by re-hosting onto AWS
  - Ex. AWS VM Import/Export
  - Ex. AWS Server Migration Service
- No cloud optimizations being done, applications are migrated as they are

## Replatforming

- Migrate some individual components to cloud services
  - Ex. database to RDS
  - Ex. application to Elastic Beanstalk
- No core architecture or applications changing

## Repurchase

- Drop and shop
- Moving to a different service while moving to the cloud

## Refactoring / Re-architecting

- Using more cloud-native services
  - Ex. serverless architecture
  - Ex. S3
- Get more features, better scalability & performance, etc.

## Retire

- Turn off things that is not need anymore
- Better security, save cost

## Retain

- Remain some components in on-premise

# Storage Gateway

- Bridge between on-premise file system and S3
- 3 types
  - File Gateway
  - Volume Gateway
  - Tape Gateway

## File Gateway

- File Gateway Appliance is a virtual machine to bridge your NFS and S3
- Metadata and directory structure are preserved
- Can be mounted on many servers using NFS or SMB protocol
- Each File Gateway should have an IAM role to access S3
- Most recently used data is cached in the File Gateway

### File Gateway Use Cases

- Read only replicas of file systems
- Backup file systems and set lifecycle policies
- Use S3 Versioning and Object Lock to protect files

## Volume Gateway

- Block storage using iSCSI protocol backed by S3
- 2 types
  - Cached volumes: full data on S3, cached in Volume Gateway
  - Stored volumes: full data is in on-premise, scheduled backups to S3
- Can create EBS snapshots from the volumes, and restore as EBS
- Up to 32 volumes per Gateway
  - Each volume up to 32 TB in cached mode
  - Each volume up to 16 TB in stored mode

## Tape Gateway

- Use Virtual Tape Library to backup files to S3 or Glacier
- Back up data using existing tape-based processes with iSCSI interface
- Cannot access single file within tapes. Need to restore the tape entirely

# Snowball

- Physical data transport solution
- Data will be loaded into S3
- Using KMS encryption
- Tracking using SNS and text messages
- Size: 50 TB and 80 TB

## Snowball Edge

- Devices have computational capability
- 100 TB capacity
- Support process data while moving
  - EC2 AMI
  - Lambda functions

## Snowmobile

- Transfer 1000 PB of data
- Better than Snowball if you transfer more than 10 PB

# Database Migration Service (DMS)

- Migrate databases to AWS
- You must create a EC2 instance to run DMS
- Support heterogeneous migrations
- Support continuous data replication using CDC
- Works over VPC Peering, VPN, Direct Connect

## DMS Sources & Targets

- Sources
  - SQL Databases in on-premise or EC2 instances
  - RDS
  - S3
- Targets
  - SQL Databases in on-premise or EC2 instances
  - RDS
  - S3
  - Redshift
  - DynamoDB
  - ElasticSearch
  - Kinesis Data Streams
  - DocumentDB

## Schema Conversion Tool (SCT)

- Convert a database's schema from one engine to another
- Support OLTP and OLAP databases

## DMS with Oracle

- Support TDE for both source and target
- Support BLOBs in tables that have a primary key

## DMS + Snowball

1. Extract data into Snowball devices, use SCT if needed
2. After AWS receives the devices, data will be loads into S3 buckets
3. Use DMS to migrate the data from S3 to any target
4. Use CDC to migrate new data coming in shipment if needed

# Application Discovery Services

- Gathering information about on-premises data centers
- Agentless discovery 
  - Use Application Discovery Agentless Connector deployed to VM host
  - Gather: VM inventory, configuration, performance
  - Works on any OS
- Agent-based discovery
  - Use Application Discovery Agent
  - Gather: configuration, performance, running processes, network connections between systems
  - Support Microsoft Server and Linux
- Results can be exported as CSV or viewed within AWS Migration Hub

# Server Migration Services (SMS)

- Migrate entire VMs to AWS
- Rehosting
- SMS is an improvement over old VM Import/Export Service
- Works with vSphere, Hyper-V, Azure VM
- Every replication creates a EBS snapshot / AMI ready for deployment on EC2
- Replication is incremental
- You can create replication one time or every interval
