---
title: 'Summary: Data Engineering'
date: 2020-08-04 12:17:00
tags: 'AWS - Solutions Architect (Summary)'
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
