---
title: 'Collection: Moving data into AWS - Kinesis'
date: 2019-07-14 09:08:57
tags: BD
categories: AWS
---

- [Introduction](#Introduction)
- [Kinesis](#Kinesis)
- [Kinesis Data Streams](#Kinesis-Data-Streams)
  - [Key Concepts](#Key-Concepts)
    - [Shards](#Shards)
    - [Records](#Records)
    - [Limits](#Limits)
  - [Kinesis Producers](#Kinesis-Producers)
    - [Kinesis Producer SDK](#Kinesis-Producer-SDK)
      - [Solutions for *ProvisionedThroughputExceeded* Exceptions](#Solutions-for-ProvisionedThroughputExceeded-Exceptions)
    - [Managed AWS Sources](#Managed-AWS-Sources)
    - [Kinesis Producer Library (KPL)](#Kinesis-Producer-Library-KPL)
    - [Kinesis Agent](#Kinesis-Agent)
  - [Kinesis Consumers Classic](#Kinesis-Consumers-Classic)
    - [Kinesis Consumer SDK](#Kinesis-Consumer-SDK)
    - [Kinesis Client Library (KCL)](#Kinesis-Client-Library-KCL)
    - [Kinesis Connector Library](#Kinesis-Connector-Library)
    - [AWS Lambda Sourcing from Kinesis](#AWS-Lambda-Sourcing-from-Kinesis)
  - [Kinesis Enhanced Fan-Out](#Kinesis-Enhanced-Fan-Out)
    - [Enhanced Fan-Out vs Standard Consumers](#Enhanced-Fan-Out-vs-Standard-Consumers)
  -[Kinesis Scaling](#Kinesis-Scaling)
    - [Shard Splitting](#Shard-Splitting)
    - [Shard Merging](#Shard-Merging)
    - [No Native Auto Scaling](#No-Native-Auto-Scaling)
    - [Limitations](#Limitations)
  - [Kinesis Security](#Kinesis-Security)
- [Kinesis Data Firehose](#Kinesis-Data-Firehose)
  - [Delivery Features](#Delivery-Features)
  - [Buffer Sizing](#Buffer-Sizing)
  - [Kinesis Data Streams vs Firehose](#Kinesis-Data-Streams-vs-Firehose)

# Introduction

- Real Time - Immediate actions
  - **Kinesis Data Streams**
  - SQS
  - IoT
- Near Real Time - Reactive actions
  - **Kinesis Data Firehose**
  - DMS
- Batch - Historical Analysis
  - Snowball
  - Data Pipeline

# Kinesis

- **Kinesis Data Streams**
  - Low latency streaming ingest at scale
- Kinesis Data Analytics
  - Perform real-time analytics on streams using SQL
- **Kinesis Data Firehose**
  - Load streams into S3, redshift, ElasticSearch and Splunk

# Kinesis Data Streams

- Streams are divided in ordered Shards
- Ability to reprocess / replay data
  - Multiple applications can consume the same stream
- Real-time processing with scale of throughput
- Once data is inserted in Kinesis, it can't be deleted (data in Kinesis is immutability)

## Key Concepts

### Shards
- The number of shards can evolve over time (reshard / merge)
- Records are only ordered per shard
- Billing is per shard provisioned

### Records
- Data Blob
  - Data being sent, serialized as bytes
  - Up to 1 MB
- Record Key
  - Helps to group records in Shards (same key in same shard)
  - Should use highly distributed key to avoid the *hot partition* problem
- Sequence number
  - Unique ID for records put in shards
  - Added by Kinesis after ingestion

### Limits
- Producer
  - 1MB/s or 1000 records/s at write per **shard**
  - *ProvisionedThroughputException* otherwise
- Consumer Classic
  - 2MB/s at read per **shard** across **all consumers**
  - 5 API calls/s per **shard** across **all consumers**
- Consumer Enhanced Fan-Out
  - 2MB/s at read per **shard** per **enhanced consumer**
  - No API calls needed
- Data Retention
  - 24 hours by default
  - Up to 7 days

## Kinesis Producers

- Kinesis Producer SDK
- Managed AWS sources
- Kinesis Producer Library (KPL)
- Kinesis Agent
- 3rd party libraries

### Kinesis Producer SDK
- PutRecord
- PutRecords
  - Use batching and increases throughput

#### Solutions for *ProvisionedThroughputExceeded* Exceptions
- Retries with backoff
- Increase (scale up) shards
- Use highly distributed partition key

### Managed AWS Sources
- CloudWatch Logs
- AWS IoT
- Kinesis Data Analytics

### Kinesis Producer Library (KPL)
- C++ / Java library
- high performance, long-running producers
- Automated and configurable **retry** mechanism
- Sync or **Async** (better performance) API
- Submits metrics to CloudWatch
- **Batching** (default) - increase throughput, decrease cost
  - **Collect** records and write to multiple shards in the same PutRecords API call
  - **Aggregate** - but increase latency
    - Store multiple records in one record (go over 1000 records/s limit)
    - Increase payload size and improve throughput (maximize 1MB/s limit)
  - Configured with *RecordMaxBufferedTime* (100ms by default)
- Limitation
  - Do not support auto compression
  - KPL Records can only be decoded with KCL or special helper library

### Kinesis Agent
- Monitor Log files and sends them to Kinesis Data Streams
- Only support Linux-based servers
- Can write form multiple directories and write to multiple streams
- Can route based on directory or log file
- Can pre-process data before sending to streams (single line, json, etc.)
- Handles file rotation, checkpointing and retry
- Can emit metrics to CloudWatch

## Kinesis Consumers Classic

- Kinesis Consumer SDK
- Kinesis Client Library (KCL)
- Kinesis Connector Library
- 3rd party libraries (Spark, etc.)
- Managed AWS sources
  - Kinesis Firehose
  - AWS Lambda

### Kinesis Consumer SDK
- GetRecords
  - Returns up to 10MB of data (then throttle for 5s) or up to 10,000 records
    - **Maximum of 2MB/s at read per shard**
    - **Maximum of 5 GetRecords API calls/s per shard (200ms latency)**

### Kinesis Client Library (KCL)
- Support Java, etc.
- Read records from Kinesis produced with the KPL (de-aggregation)
- **Shard discovery**: share multiple shards with multiple consumers in one group
- **Checkpointing**: resume progress
- **Leverages DynamoDB for coordination and checkpointing**
  - Use provisioned DynamoDB with enough WCU and RCU
  - Use On-Demand DynamoDB
  - Otherwise DynamoDB may slow down KCL

### Kinesis Connector Library
- Have been deprecated
- Can only be running on EC2
- Write data to S3, DynamoDB, Redshift, ElasticSearch
- Can be replaced by Kinesis Firehose or Lambda

### AWS Lambda Sourcing from Kinesis
- Lambda consumer has a library to de-aggregate record from the KPL
- Lambda can be used to rum lightweight ETL to anywhere you want
- Lambda can be used to trigger notifications or send emails in real time
- Lambda has a configurable batch size to regulate throughput

## Kinesis Enhanced Fan-Out

- Kinesis **pushes** data to consumers over HTTP/2 
- **Each Consumer** get 2 MB/s of providioned through per shard
  - No more 2MB/s limit by adding Consumers
  - Reducy latency (~70ms)

### Enhanced Fan-Out vs Standard Consumers
|Enhanced Fan-Out|Standard Consumers|
|---|---|
|Low number of consuming applications|Multiple Consumer applications for the same Stream|
|Can tolerate ~200 ms latency|Low latency requirements ~70 ms|
|Minimize cost|Higher costs (Soft limit of 5 consumers using enhanced fan-out per data stream)|

## Kinesis Scaling

### Shard Splitting
- Can be used to increase the Stream capacity (1MB/s per shard)
- Can be used to divided a *hot shard*
- The old shard is closed and deleted based on data expiration

### Shard Merging
- Decrease the Stream capacity and save costs
- Group two shards with low traffic
- Old shards are closed and deleted based on data expiration

### No Native Auto Scaling
- Can implement Auto Scaling with AWS Lambda
- The API call to change the number of shards is *UpdateShardCount*

### Limitations
- Resharding cannot be done in parallel (have to plan capacity in advance)
- Can only perform one resharding at a time and it takes a few seconds
  - Double 1000 shards takes about 8.3 hours

## Kinesis Security

- Control access / autorization using IAM policies
- Encryption in transit using HTTPS endpoints
- Encryption at rest using KMS
- Client side encryption can only be implemented manually
- VPC Endpoints is available for Kinesis

# Kinesis Data Firehose

- Near Real Time (60s latency minimum for non-full batches)
- Read data from
  - SDK or KPL
  - Kinesis Agent
  - Kinesis Data Streams
  - CloudWatch Logs & Events
  - IoT rules actions
- Load data into
  - **S3**
  - **Redshift**
  - **ElasticSearch**
  - **Splunk**
- Spark or KCL **cannot** read from Firehose
- Auto scaling
- Support Data Conversions
  - JSON to Parquet
  - JSON to ORC (only for S3)
- Support Data Transformation through AWS Lambda
- Support Support compression (only for S3)
  - GZIP, ZIP, Snappy
  - Only GZIP when data is further loaded into Redshift
- Only pay for the amount of data going through Firehose

## Delivery Features
- Source Records
- Transformation Failures
- Delivery Failures

can be stored directly into S3

## Buffer Sizing
- When buffer is reached, it's flushed
  - Buffer Size
  - Buffer Time
- Automatically increase the buffer size to increase throughput
- High through: more buffer size
- Low through: less buffer time

## Kinesis Data Streams vs Firehose
|Streams|Firehose|
|---|---|
|Custom consumer code|Fully managed, only send to S3, Redshift, ElasticSearch, Splunk|
|Real time (~200ms for classic or ~70ms for enhanced fan-out)|Near real time (1min~ buffer time)|
|Manual Scaling (shard splitting / merging)|Auto Scaling|
|Data Storage for 1 to 7 days|No data storage|
