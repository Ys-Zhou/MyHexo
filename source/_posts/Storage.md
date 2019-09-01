---
title: 'Storage'
date: 2019-08-17 10:48:03
tags: AWS - Big Data
categories:
---

# S3

## Storage Tiers
- S3 Standard - General Purpose
- S3 Standard - Infrequent Access (IA)
- S3 One Zone - Infrequent Access (IA)
- S3 Glacier
  - Standard & Glacier: span at least 3 AZs
- S3 Intelligent Tiering
  - Auto Tiering

## Lifecycle Rules
- Transition actions (move between tiers)
- Expiration actions (delete)

## Versioning
- Enabled at the bucket level

## Cross Region Replication
- Must enable versioning
- Can be in different accounts
- Asynchronous
- Need proper IAM permissions

## Etag (Entity Tag)
- Simple upload
  - Etag = MD5
- Multi-part upload
  - Etag is calculated by the algorithm
- Use Etag to ensure integrity

## Performance
- Key prefix
  - 3500 RPS for PUT and 5500 RPS for GET
  - Use random, do not use date
- Multi-part upload
  - Faster when object &gt; 5 GB
  - Decrease time to retry
- CloudFront to cache S3 objects
- S3 Transfer Acceleration (use edge location)
- If using SSE-KMS, limits of KMS is 100 -1000 downloads / uploads per second

## Encryption
| |SSE-S3|SSE-KMS|SSE-C|CSE|
|---|---|---|---|---|
|Encryption key is managed by|S3|KMS|Customer|Customer|
|*x-amz-server-side-encryption* header|aes256|aws:kms|${encryption key}|-|
|HTTPS (SSL / TLS)|Optional|Optional|Mandatory|Optional|
|Can be set as default encryption|Yes|Yes|No|No|

## Security
### S3 CORS (Cross-Origin Resource Sharing)
- Enable CORS if you want to request data from website in different origin
- Can control the websites that can request your objects in S3

### S3 Access Logs
- Log all events that access to S3 bucket

### Policies and ACL
- IAM policies
- Bucket Policies
- Bucket ACL (Bucket Policies are better)
- Object ACL

### Others
- VPC Endpoint
- MFA can be required in versioned buckets when delete objects
- Presigned URL

## Glacier
- Retrieval options
  - Expedited (1 to 5 minutes)
  - Standard (3 to 5 hours)
  - Bulk (5 to 12 hours)
- Vault Access Policies
- Vault Lock Policies
  - Immutable policies

# DynamoDB

- Distributed NoSQL database
- DynamoDB
  - tables
    - primary key (Unique and diverse)
      - Partition key only
      - Partition kay and sort key
    - items (Max size is 400 KB)
      - attributes
        - String, Number, Binary, Boolean, NUll
        - String Set, Number Set, Binary Set
        - List, Map

## Provisioned Throughput
- Read and write capacity units must be provisioned
  - Option to setup auto-scaling of throughput
- Throughput can be exceeded temporarily using *burst credit*
- If capacity is exceeded, you will get *ProvisionedThroughputExceededExceptions*
  - It is advised to do an exponential back-off retry

### Write Capacity Units (WCU)
- 1 WCU = 1 write per second for an item up to 1 KB
- **WCU = (number of objects per second) * ceil(max size of object / 1 KB)**

### Strongly Consistent Read vs. Eventually Consistent Read
- DynamoDB is distributed
  - Uses Eventually Consistent Read by default
- Strongly Consistent Read
  - Will get correct data if read just after write
- Eventually Consistent Read
  - Maybe get outdated data if read just after write because of replication

### Read Capacity Units (RCU)
- 1 RCU = 1 strongly consistent read or 2 eventually consistent reads per second for an item up to 4 KB
- Strongly Consistent Read
  - **RCU = (number of objects per second) * ceil(max size of object / 4 KB)**
- Eventually Consistent Read
  - **RCU = (number of objects per second / 2) * ceil(max size of object / 4 KB)**

### *ProvisionedThroughputExceededExceptions*
- Occurred when RCU or WCU is exceeded
- Reasons
  - Hot keys or partitions
  - Too large items
- Solutions
  - Use exponential back-off in SDK
  - Distribute partition keys
  - If it is RCU issue, you can use DynamoDB Accelerator (DAX)

## Partition
- Each partition
  - Max of 3000 RCU and 1000 WCU
  - Max of 10 GB
- The number of partition
  - Capacity partitions = ceil((RCU / 3000) + (WCU / 1000))
  - Size partitions = ceil(Size / 10 GB)
  - Partitions = max(Capacity partitions, Size partitions)
- WCU and RCU are spread evenly between partition

## APIs
- *PutItem*
  - Creat data or full replace
- *UpdateItem*
  - partial update
- *ConditionalWrites*
  - Put / update only if conditions are respected
  - No performance impact
- *DeleteItem*
  - Delete an individual row
  - Can perform a conditional delete
- *DeleteTable*
  - Delete a whole table
- *BatchWriteItem*
  - Up to 25 *PutItem* and / or *DeleteItem* in one call
  - Up to 16 MB and up to 400 KB per item
  - Lower latency by reducing API calls
  - Batching is done in parallel
  - Batching also supports exponential back-off retry
- *GetItem*
  - Read based on Primary key
    - Partition key
    - Partition key + sort key
  - Eventually consistent read by default (option to use strong consistent read)
  - Can only **filter** data by Projection Expression
- *BatchGetItem*
  - Up to 100 items
  - Up to 16 MB
  - Batching is done in parallel

### Query
- Based on
  - Partition key = &lt;condition&gt; (demand)
  - Sort key [=, &lt;, &lt;=, &gt;, &gt;=, Between, Begin] &lt;condition&gt; (optional)
  - Limit &lt;number of items&gt;
  - Client side filtering by Filter Expression
- Returns
  - Up to 1 MB of data or use pagination

### Scan
- Scan entire table
- Returns up to 1 MB of data or use pagination
- Can reduce consumption of RCU by
  - using limit
  - pausing
- Filter data use Projection Expression or Filter Expression
- **parallel scans**
  - Can scan multiple partitions at the same time
  - Increases throughput but also RCU consumed