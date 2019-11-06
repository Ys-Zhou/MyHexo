---
title: Analysis
date: 2019-10-30 08:50:00
tags: AWS - Big Data
---

# Kinesis Analytics

- Querying streams of data continuously
- Can receive data from and send results to
  - Kinesis Data Streams
  - Kinesis Data Firehose
- Analysis using SQL
- Can use reference table from S3 to help analysis
- Errors will be output to Error Stream

## Use-cases
- Streaming ETL
- Continuous metric generation
- Responsive analytics

## Schema discovery
- Generate data schema automatically by feeding some stream data

## RANDOM_CUT_FOREST
- A SQL function offered by Kinesis Data Analytics
- For anomaly detection

# Amazon Elasticsearch Service (ES)

- Petabyte-scale search, analysis, and reporting tools
- Fundamentally based on a search engine (Lucine)
- ES can be regarded as a analysis tool
  - Has a visualization tool (Kibana)
- Can use data pipeline to send stream data to ES
  - Kinesis
  - Beats, LogStash, Apache Kafka
  - ES API
- Horizontally scalable

## Use Cases
- Text search
- Log analytics
- Application monitoring
- Security analytics
- Click stream analytics

## Concepts
- Documents
  - Text or JSON
- Types
  - Define th schema and mapping shared by documents
- Indices
  - Power search into all documents within a collection of types
  - An index is split into shard
    - A primary shard and replicas
    - Write requests are routed to the primary shard, then replicated
    - Read requests are routed any shards

## Features
- Full-managed but not serverless, ES runs based on EC2
  - Can scale up or down without downtime but should it manually
- Network isolation (VPC)
  - Can use all the features of VPC
- AWS integration
  - AWS IoT
  - S3 via Lambda to Kinesis
  - Kinesis Data Streams
  - DynamoDB Streams

## Options
- Dedicated master node(s)
  - Only used for the management and dose not hold or process data
  - Decide numbers and instance types
- Domains
  - A collection of all the resources of a ES cluster
- Automatic snapshots to S3

## Security
- Resource, identity, IP-based policies
- Request signing
- VPC
- Cognito

## Anti-patterns
- OLTP
  - RDS or DynamoDB
- Ad-hoc data querying
  - Athena

# Amazon Athena

- Interactive query service for S3 in SQL
- Use Presto
- Supported data formats
  - CSV
  - JSON
  - ORC (columnar, splittable)
  - Parquet (columnar, splittable)
  - Avro (splittable)

## Athena Integration
- Jupyter, Zeppelin, RStudio notebooks
- QuickSight
- Other visualization tools via ODBC / JDBC

## Athena with Glue
- Use Glue to define unstructured data in S3

## Athena Cost Model
- Successful and cancelled queries count, failed queries not
- No charge for DDL
- Save money by using columnar formats
  - ORC, Parquet
  - And better performance
- Glue and S3 charge separately

## Athena Security
- Access control
  - IAM, ACLs, S3 bucket policies
  - Cross-account access in S3 bucket policy possible
- Encrypt results at rest in S3 staging directory
- TLS encrypts in transit

## Athena anti-patterns
- Highly formatted reports / visualization
  - Use QuickSight
- ETL
  - Use Glue

# Amazon Redshift

- Fully-managed, petabyte scale data warehouse service
- Designed for OLAP, not OLTP
- SQL, ODBC, JDBC interfaces just like other RDB
- Easily scale up and down manually
- Built-in replication & backups

## Redshift architecture
- Redshift Cluster
  - A leader node
    - Manage communication with clients & compute nodes
    - Receives queries form clients & develops execution plans
    - Coordinates the parallel execution of those plans with compute nodes
    - Aggregates the intermediate results from compute nodes
  - 1~128 compute nodes
    - Store user data
    - Execute the steps in the execution plans
    - Can transmit data among themselves
    - Node types
      - Dense storage (DS): xl or 8xl
        - HDD
        - Low costs
      - Dense compute (DC): xl or 8xl
        - SSD
        - Larger memory
        - Faster CPU
    - Slices
      - Each compute node is divided into slices
      - A slice allocates a portion of the memory and disk storage of the node
      - Size of slices is determined by the node size of the cluster 
      
## Redshift Spectrum
- Query unstructured data in S3 like Redshift table without loading
- Limitless concurrency & horizontal scaling
- Support wide variety of data formats
- Support Gzip and Snappy compression

## Redshift Performance
- Massively Parallel Processing (MPP)
- Columnar Data Storage
- Column Compression

## Redshift Durability
- Redshift has 3 copies of data
  - An original copy within the cluster
  - A backup repica copy within the cluster
  - Continuously backed up to S3
    - Can furthermore asynchronously replicated to another region
      - Default retention period is 1 day, up to 35 days, 0 to turn off
- Redshift will mirror each drive's data to another nodes within the cluster if there are 2 or more compute nodes
  - Can detect a failed drive or node, and replace if automatically
  - Drive failure
    - Redshift will remain available but performance may be declined
  - Node failure
    - Redshift will be unavailable during recovery
    - Most frequently access data from S3 is loaded first which you can querying as quickly as possible
- Redshift is limited to a single AZ
  - You have to restore the data from S3 in a different AZ when facing AZ failure

## Redshift Scaling
- Vertical and horizontal scaling on demand
- During scaling
  - A nwe cluster is created while old cluster remains available for reads
  - CNMAE is flipped to new cluster with a few minutes of downtime
  - Data moved in parallel to new compute nodes
