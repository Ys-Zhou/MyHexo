---
title: 'Services: Data Engineering'
date: 2020-08-04 12:17:00
tags: 'AWS - Solutions Architect (Services)'
---

# Kinesis

- Kinesis Streams: low latency streaming ingest at scale
- Kinesis Analytics: real-time analytics on streams using SQL
- Kinesis Firehose: load streams into S3, Redshift, ElasticSearch, Splunk

## Kinesis Streams

- Can reprocess data from streams
- Multiple applications can consume a same stream
- Easy to scale
- Data cannot be deleted from Kinesis

### Kinesis Streams Shards

- Streams are divided in Shards
- Can send or retrieve data in batch
- Can reshard or merge shards
- Records are ordered in a shard, shards are not in order

### Kinesis Producers

- AWS SDK
- KPL
  - With batch, compression, retries
- Kinesis Agent

### Kinesis Consumers

- AWS SDK
- Lambda through Event Source Mapping
- KCL
  - With checkpointing

### Kinesis Streams Limits

- Producer
  - 1 MB/s or 1000 messages/s per Shard
- Consumer Classic
  - 2 MB/s per Shard across all consumers
  - 5 API calls per second per shard across all consumers
- Consumer Enhanced Fan-Out
  - 2 MB/s per Shard per consumer
  - No API call needed
- Data Retention
  - 24 hours by default, up to 7 days

## Kinesis Firehose

- Managed service with auto-scaling, serverless
- Near real time (60 seconds latency at least)
- Load data into S3, Redshift, ElasticSearch, Splunk
- Integrate with Lambda to transform data

### Firehose Buffer Sizing

- The buffer is flushed based on size and time rules
- Buffer size
  - Firehose can automatically increase the buffer size to increase throughput
- Buffer time

## Kinesis Analytics

- Managed service with auto-scaling, serverless
- Use SQL or Flink
- Lambda can be used for pre processing
- Use cases
  - Streaming ETL
  - Continuous metric generation
  - Responsive analytics

# AWS Batch

- Run batch jobs as Docker images
- Serverless
  - Batch -> ECS -> EC2 in VPC
    - Make sure that EC2 instances have access to ECS
    - If EC2 instances are in private subnet, ether use a NAT gateway / instances or use VPC endpoints for ECS 
  - Batch is free, you just pay for underlying EC2 instances
- Integration
  - Schedule Batch jobs using CloudWatch Events
  - Orchestrate Batch jobs using Step Function

## Batch Compute Environments

- Managed Compute Environment
  - Batch manage the capacity and instance types
  - You can choose On-Demand or Spot instances
    - You can set a maximum price for spot instances
- Unmanaged Compute Environment
  - You control and manage instance configuration, provisioning, scaling

## Batch Multi-Node Mode

- Leverage multiple EC2 instances
- One main node, multiple child nodes
- Cannot use Spot instances
- Have better performance if EC2 instances lunch in a placement group

# EMR

- AWS managed Hadoop clusters
- EMR takes care of provisioning and configuration of EC2
- Auto-scaling with CloudWatch

## EMR Storage

- HDFS
  - EBS
  - Temporary
  - Single AZ (because a cluster is in a single AZ)
- EMRFS
  - S3
  - Permanent
  - Multi-AZ
- Hive can read from DynamoDB

## EMR Node Types

- Master Node: manage cluster
- Core Node: run tasks and store data
- Task node: only run tasks
- Any type of node can use a configuration of EC2
  - On-demand instances
  - Reserved instances
  - Spot instances

# Redshift

- Data warehouse
- Columnar storage
- Not serverless, need provisioning instances
- Run queries by SQL
- Quicksight and Tableau integration
- Load data from D3, Kinesis Firehose, DynamoDB, DMS, etc.
- Enhanced VPC Routing: run COPY / UNLOAD commands through VPC

## Redshift Nodes

- Up to 128 nodes per cluster
- Up to 160 GB per node
- All nodes (cluster) are in a single AZ
- Leader node: planning queries, aggregating results
- Compute nodes: performing queries

## Redshift Snapshots

- Snapshots are stored internally in S3
- Snapshots are incremental
- Snapshots can be restored into a new cluster
- Automated snapshots
  - Every 8 hours or 5 GB
  - Can set a schedule and retention
- Manual snapshots
  - Also need to be deleted manually
- Can configure Redshift to automatically copy snapshots to another Region

## Redshift Spectrum

- Query data in S3 without loading it using Redshift
- Must have a Redshift cluster to start queries
- Queries will run in AWS managed Redshift Spectrum nodes

# Athena

- Queries data in S3 using SQL
- Supports CSV, JSON, Parquet, ORC
- Queries are logged in CloudTrail

# Quicksight

- BI tool for data visualization, creating dashboards
- Integrates with Athena, Redshift, EMR, RDS
