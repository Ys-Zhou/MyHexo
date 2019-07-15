---
title: 'Collection: Moving data into AWS'
date: 2019-07-14 09:08:57
tags: BD
categories: AWS
---

## Introduction

- Real Time - Immediate actions
  - Kinesis Data Streams
  - SQS
  - IoT

- Near-real time - Reactive actions
  - Kinesis Data Firehose
  - DMS

- Batch - Historical Analysis
  - Snowball
  - Data Pipeline

## Kinesis

- Kinesis Data Streams
  - Low latency streaming ingest at scale
- Kinesis Data Analytics
  - Perform real-time analytics on streams using SQL
- Kinesis Data Firehose
  - Load streams into S3, redshift, ElasticSearch and Splunk

## Kinesis Streams

- Streams are divided in ordered Shards
- Ability to reprocess / replay data
  - Multiple applications can consume the same stream
- Real-time processing with scale of throughput
- Once data is inserted in Kinesis, it can't be deleted (data in Kinesis is immutability)

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
  - 1MB/s or 1000 messages/s at write per **shard**
  - *ProvisionedThroughputException* otherwise
- Consumer Classic
  - 2MB/s at read per **shard** across **all consumers**
  - 5 API calls per second per **shard** across **all consumers**
- Consumer Enhanced Fan-Out
  - 2MB/s at read per **shard** per **enhanced consumer**
  - No API calls needed
- Data Retention
  - 24 hours by default
  - Up to 7 days

## Kinesis Producers

- Kinesis Producer SDK
- Kinesis Producer Library (KPL)
- Kinesis Agent
- 3rd party libraries

### SDK
- PutRecord
- PutRecords
  - Use batching and increases throughput

#### Managed AWS sources
- CloudWatch Logs
- AWS IoT
- Kinesis Data Analytics

#### Solutions for *ProvisionedThroughputExceeded* Exceptions
- Retries with backoff
- Increase (scale up) shards
- Use highly distributed partition key

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
- Can pre-process data before sending to streams (single line, json...)
- Handles file rotation, checkpointing and retry
- Can emit metrics to CloudWatch

## Kinesis Consumers Classic

