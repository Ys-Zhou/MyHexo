---
title: Security
date: 2019-11-11 09:33:00
tags: AWS - Big Data
---

# AWS KMS (Key Management Service)

- Encryption for AWS services
- Fully integrated with IAM for authorization
- Integration into
  - EBS: encrypt volumes
  - S3: SSE-KMS of objects
  - Redshift / RDS: encrypt data
  - Etc.
- Can use KMS using CLI / SDK

## About KMS
- Keys (CMK) in KMS can never be retrieved by users
- KMS can only encrypt up to 4KB of data per call
  - If data &gt; 4 KB, use client-side encryption
- To give access to KSM to a user
  - Key Policy allows the user
  - IAM Policy allows the API calls

## KMS Detail
- Able to fully manage the keys and policies
  - Create
  - Rotation policies
  - Disable
  - Enable
- Able to audit key usage using CloudTrail
- Three types of Customer Master Keys (CMK)
  - AWS Managed Service Default CMK: free
  - User Keys created in KMS: $1 / month
  - User Keys imported (256-bit symmetric): $1 / month
- Each API call to KMS: $0.03 / 10000 calls
  - Encrypt API
  - Decrypt API

## Encryption Using KMS
- Requires migration (Snapshot / Backup)
  - EBS Volumes
  - RDS databases
  - ElastiCache
  - EFS file system
- In-place encryption
  - S3

# CouldHSM

- HSM = Hardware Security Module
- Dedicated encryption hardware
- Users manage their keys (not AWS)
- FIPS I40-2 Level 3 compliance
- CloudHSM clusters are spread across multi-AZ
- Support both symmetric and asymmetric encryption
- Must use the CloudHSM Client Software

## KSM vs. CloudHSM
Feature|KSM|CloudHSM
---|---|---
Tenancy|Multi-tenant key storage|Single dedicated tenant key storage
Keys|Keys managed by AWS|Customer managed HSM
Encryption|Symmetric encryption only|Symmetric and asymmetric encryption
Cryptographic Acceleration|None|SSL/TLS Acceleration & Oracle TDE Acceleration
Key Storage and Management|Accessible from multiple regions. Centralized management from IAM|Deployed and managed form a customer VPC. Accessible and can be shared across VPCs using VPC peering

# Security in AWS Services

## Kinesis
- Kinesis Data Streams
  - SSL/TLS
  - KMS
  - Client-side encryption can only be implemented manually
  - IAM roles
    - **DynamoDB table when using KCL**
  - Support VPC Endpoints
- Kinesis Data Firehose
  - Can encrypt the delivery stream with KMS
  - IAM roles
    - Deliver to S3, ES, Redshift, Splunk
  - Support VPC Endpoints
- Kinesis Data Analytics
  - IAM roles
    - Sources and destination (Kinesis Data Streams, Kinesis Data Firehose)

## SQS
- SSL/TLS
- KMS
- Client-side encryption can only be implemented manually
- IAM roles
- **SQS queue access policy**
- Support VPC Endpoints

## AWS IoT
- IoT policies
  - Attached to X.509 or Cognito Identities
  - Can be attached to groups as well as individual Things
- IAM
  - Used for controlling IoT APIs
  - Attach roles to Rules Engine to allow their actions
