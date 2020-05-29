---
title: 'Summary: Security'
date: 2020-05-29 15:19:28
tags: 'AWS - Solutions Architect'
---

# CloudTrail

- Provides governance, compliance and audit for your AWS account
- CloudTrail is enabled by default
- Get a history of events / API calls made within your AWS account
- Can put logs from CloudTrail to CloudWatch Logs
- CloudTrail console only shows the past 90 days of activity
- The default UI only shows *Create*, *Modify*, and *Delete* events

## CloudTrail Trail

- Get a detailed list of all the events you choose
- Can include events happing at the object level in S3
- Ability to store these events in S3 for further analysis
- Can be region specific or be global

## Architecture: Delivery to S3

- CloudTrail can send events to S3 every 5 minutes
- Use SSE-S3 encryption by default or SSE-KMS
- Notification
  - Use CloudTrail Delivery Notifications
    - Send notifications to SNS, can further send to SQS or Lambda
  - Use S3 Events
    - Send S3 events to SQS, SNS, or Lambda
- All benefits of S3
  - Versioning
  - MFA Delete protection
  - Lifecycle Policy
  - Object Lock
  - Encryption (SSE-S3, SSE-KMS)
  - SHA-256 for integrity validation

## Architecture: Multi-Account, Multi-Region Logging

- Send CloudTrail events from different accounts to S3 bucket in one account
- S3 bucket policy is necessary for cross-account delivery
- Two options to access log files from other accounts
  1. Assume cross-account role
  2. Edit bucket policy to allow cross-account access

## Architecture: Alert for API calls

- Stream CloudTrail events to CloudWatch Logs
- Create Metric Filters that trigger CloudWatch Alarm
- CloudWatch Alarm can send notifications to SNS

## Delivery Time

- Overall
  - 15 minutes
- CloudWatch Events
  - Can be triggered for any API call in CloudTrail
  - The fastest way
- CloudTrail Delivery in CloudWatch Logs
  - Events are streamed
- CloudTrail Delivery in S3
  - Events are delivery every 5 minutes

# AWS KMS

- Encryption for AWS Services
  - EBS: encrypt volumes
  - S3: SSE-KMS
  - Redshift
  - RDS
  - SSM: Parameter Store
  - ect.
- Encrypt your own data
  - KMS APIs (Encrypt / Decrypt)
- CMK can never be retrieved bu user
- CMK can be rotated for extra security
- KMS can only encrypt up to 4KB of data per call
- To access KMS
  - Make sure the KEY Policy allows the user
  - Make sure the IAM Policy allows the API calls
- CloudTrail tracks API calls made to KMS

## Types of CMK

- Customer Managed CMK
  - Users can create, manage, use, or enable/disable the Keys
  - Possibility of rotation policy
    - New key generated every year
    - Old key will be preserved
  - Can add key policy
- AWS Managed CMK
  - Used by AWS services (aws/s3, aws/ebs, aws/redshift, ect.)

# SSM Parameter Store

- Secure storage for configuration and secrets
- Secrets can be encrypted by KMS
- Support version tracking
- Secrets are stored in path
- Notifications with CloudWatch Events
- Integration with CloudFormation
- Can retrieve secrets from Secrets Manager using the SSM Parameter Store API

# Secrets Manager

- A newer service for storing secrets
- Capability to force rotation of secrets every X days
- Can automatically generate secrets on rotation using Lambda
- Integration with **RDS** (MySQL, PostgreSQL, Aurora)
- Encrypted using KMS

# RDS Security

- KMS encryption at rest for underlying EBS volumes and snapshots
- Transparent Data Encryption (TDE) for Oracle and SQL Server
- SSL encryption for all kinds of DB
- IAM authentication for MySQL and postgreSQL
  - Authorization still happens within RDS
- Can copy an un-encryption RDS snapshot into an encrypted one
- CloudTrail cannot be used to track queries made within RDS
