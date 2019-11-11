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
- KMS can only encrypt up to 4KB of data per call
  - If data &gt; 4 KB, use client-side encryption
- To give access to KSM to a user
  - Key Policy allows the user
  - IAM Policy allows the API calls
