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
