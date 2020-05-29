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
