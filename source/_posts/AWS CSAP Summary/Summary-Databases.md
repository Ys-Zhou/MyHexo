---
title: 'Summary: Databases'
date: 2020-08-02 22:00:38
tags: 'AWS - Solutions Architect (Summary)'
---

# DynamoDB

- Object size is up to 400 KB
- Can provision, auto or on-demand scale WCU and RCU
- Can configure eventually or strong consistent reads
- Support ACID
- Support backups and point in time recovery
- Data types
  - String, Number, Binary, Boolean, Null
  - List, Map
  - Set

## DynamoDB Primary Keys

- Partition key only
- Partition key + Sort key

## DynamoDB Index

- LSI
  - Another sort key + the same partition
  - Must be defined at table creation time
- GSI
  - Another partition key + optional sort key
  - Can be defined after the table is created
- Tables can only be queried by primary key and indexes

## DynamoDB Streams

- React to changes in DynamoDB tables in real time
- Can be read by Lambda, EC2, etc.
- 24 hors retention of data in stream

## DynamoDB Global Tables

- Active-active replication across regions
- Must enable DynamoDB Streams

## DAX

- Cache for DynamoDB
- Reads and writes go through DAX to DynamoDB
- 5 minutes TTL by default
- Up to 10 nodes in the cluster
- Multi-AZ

### DAX vs. ElastiCache

- DAX can only cache DynamoDB query results
- ElastiCache can cache any data

## Solution Architecture: Indexing S3 objects in DynamoDB

- Use S3 Event to trigger Lambda function to write metadata into DynamoDB
- You can use metadata in DynamoDB to search objects

# ElasticSearch (ES)

- AWS managed ElasticSearch
- Based on EC2, not serverless
- Use cases
  - Log analytics
  - Application monitoring
  - Text search
  - Indexing

## Elastic Stack

- ElasticSearch
  - Provide search and indexing capability
- Kibana
  - Provide real-time dashboards on top on the data in ES
- Logstash
  - Provide log ingestion

## ElasticSearch Integrations

- DynamoDB
  - Use DynamoDB streams to trigger Lambda function to write items into ES for search
- CloudWatch Logs
  - Use Lambda function or Kinesis Firehose to write logs into ES

# RDS

- Engines
  - PostgreSQL
  - MySQL
  - MariaDB
  - Oracle
  - Microsoft SQL Server
- Launched within a VPC
- Storage by EBS
  - Can use auto-auto-scaling
- Automated with point-in-time recovery
- Manual snapshots
- RDS Events

## RDS Multi-AZ vs. Read Replicas

- Multi-AZ (Failover)
  - Read from and write to master database
  - Sync replication
  - In one region
- Read Replicas
  - Can read from Read Replicas
  - Async replication
  - Can be cross-region

## RDS Security

- EBS volumes and snapshots are encrypted by KMS
- Transparent Data Encryption is available for Oracle and Microsoft SQL Server
- SSL
- IAM authentication is available for MySQL and PostgreSQL
  - Authorization still happens within RDS
- Can copy an un-encrypted RDS snapshot into an encrypted one
- CloudTrail cannot track RDS queries

## Aurora

- PostgreSQL-compatible and MySQL-compatible DB engine
- Automatically grow up to 64 TB
- Six copies of data in 3 AZ
  - Automated failover for master in less than 30 seconds
- Up to 15 read replicas (support cross-region read replicas)
- Can load / offload data directly from / to S3

### Aurora Serverless

- Automated database instantiation and auto-scaling based on actual usage
- Good for infrequent, intermittent or unpredictable workloads
- No capacity planning needed
- Pay per second

### Global Aurora

- Cross-Region Read Replicas
  - Up to 15 read replicas
- Global Aurora (recommended)
  - One primary region (read / write)
  - Up to 5 secondary regions (read), lag is less than 1 second
  - Up to 16 read replicas each secondary region
  - Promoting another region has a RTO that less than 1 minute

### Aurora Multi-Master

- Immediate failover
- Every node does Read / Write
