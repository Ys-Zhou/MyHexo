---
title: 'Collection'
date: 2019-07-14 09:08:57
tags: AWS - Big Data
categories:
---

# Kinesis

- Kinesis Data Streams
  - Low latency streaming ingest at scale
- Kinesis Data Analytics
  - Perform real-time analytics on streams using SQL
- Kinesis Data Firehose
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
- Support compression (only for S3)
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
- High throughput: more buffer size
- Low throughput: less buffer time

## Kinesis Data Streams vs Firehose
|Streams|Firehose|
|---|---|
|Custom consumer code|Fully managed, only send to S3, Redshift, ElasticSearch, Splunk|
|Real time (~200ms for classic or ~70ms for enhanced fan-out)|Near real time (1min~ buffer time)|
|Manual Scaling (shard splitting / merging)|Auto Scaling|
|Data Storage for 1 to 7 days|No data storage|

# AWS SQS

## SQS - Standard Queue
- Auto Scales without limit
- Retention of messages from 1 minute to 14 days, 4 days by default
- the number of messages in the queue has no limit
- Low latency (~10 ms on publish and receive)
- Consumers can be scaled horizontally
- **Can have duplicate messages**
- **Can have out of order messages**
- **Limitation of 256KB per message**

## SQS - FIFO Queue
- Name of the queue ends in .fifo
- Up to 3,000 messages/s with batching or 300 messages/s without (soft limit)
- **Messages are sent only once**
- **Messages are processed in order**

## Producing Messages
- Message body
  - String
  - Up to 256KB
- Message attributes (metadata)
  - Key-value pair
- Delivery delay
- Return
  - Message ID
  - MD% hash of the body

## Consuming Messages
- Poll messages from SQS 
  - Up to 10 messages at a time
- Process the message within the visibility timeout
- Delete the message by message ID & receipt handle
- Maximum of 120,000 messages being processed by consumers

## SQS Extended Client
- Send messages larger than 256KB
- Not recommended
- Java Library
  - Send message to S3 first
  - Only send metadata to SQS

## Use Cases
- Decouple applications asynchronously
- Buffer writes
- Handle large loads of messages

## Pricing
- Pay per API request
- Pay network usage

## Security
- HTTPS endpoints
- Server Side Encryption using KMS
  - Only encrypts the body
- IAM
- SQS queue access policy

# AWS IoT

## Device Gateway
- The entry point for IoT devices connecting to AWS IoT
- Supports the MQTT, WebSockets, and HTTP 1.1 protocols

## Message Broker
- For devices to communicate with others
- Pub/sub messaging pattern with low latency
- Supports the MQTT, WebSockets, and HTTP 1.1 protocols

### Rules Engine
- Rules are defined on the Broker topics
- Rule-action pairs
- Used to augment or filter data received from devices
- Rules need IAM Roles to perform the actions

## Thing Registry
- Represent all connected devices
  - A unique ID
  - Metadata
  - X.509 certificate
  - IoT Groups
- Can organizes the resources associated with each device

### Authentication
- Things
  - X.509 certificates
  - AWS SigV4
  - Custom tokens with custom authorizers
- Mobile apps
  - Cognito
- Web/Desktop/CLI
  - IAM
  - Federated Identities

### Authentication Policies
- IoT policies
  - Attached to X.509 certificates or Cognito Identities
  - Can be attached to Groups in stead of Things
- IAM policies
  - Used for controlling IoT AWS APIs

## Device Shadow
- Record the state of Things and synchronize the state when Things back up online
- JSON document

## Greengrass
- Brings the compute layer to the devices locally
- Execute AWS Lambda on the devices and operate offline
- Deploy functions from the cloud

# Database Migration Service (DMS)

- Migrate databases to AWS
- The source database remains available during the migration
- Must run an EC2 instance to perform Continuous Data Replication task using Change Data Capture (CDC)

## Sources and Targets
- Sources
  - On-Premise or EC2 instances databases
  - RDS
  - S3
- Targets
  - On-Premise or EC2 instances databases
  - RDS, Redshift, DynamoDB
  - S3
  - ElasticSearch, Kinesis Data Streams, DocumentDB

## Schema Conversion Tool (SCT)
- A tool to convert database schema from one to another
- Can use SCT to create DMS endpoints and tasks

## Different between DMS and SCT
|DMS|SCT|
|---|---|
|Migrate smaller relational workloads (&lt; 10 TB) and MongoDB|Migrate large data warehouse workloads|
|Support ongoing replication to keep the target in sync|Do not support|

# Direct Connect

- Provides a dedicated private connection from a remote network to your public resources or VPCs
  - Need a Virtual Private Gateway on your VPC
- Can setup multiple 1 Gbps or 10 Gbps connections
- Connect to Direct Connect endpoint in Direct Connect location
- Use Cases
  - Increase bandwidth throughput
  - Real-time data feeds
  - Hybrid Environment
  - Enhanced security using private connection
- Use two Direct Connect as a failover
- Use Direct Connect Gateway if you want to connect to VPCs in different regions

# Snowball

- Physical device to move TBs to PBs of data in or out of AWS
- Secure by using KMS 256 bit encryption
- Use Snowball if it takes more than a week to transfer over the network

## Snowball Edge
- More capacity (100 TB) with computational capability
  - Storage optimized
  - Compute optimized
- Can pre-process the data while moving
  - Support EC2 AMI
  - Support Lambda functions

## Snowmobile
- 100 PB of capacity
- Batter than Snowball if more than 10 PB
