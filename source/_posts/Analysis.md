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

