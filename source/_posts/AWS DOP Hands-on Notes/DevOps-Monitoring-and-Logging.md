---
title: 'DevOps: Monitoring and Logging'
date: 2022-03-07 22:47:33
tags: AWS - DevOps Engineer
---

# CloudTrail

## CloudTrail Trails

- Can be applied to specific region or all regions
- Event types: All, Read-only, Write-only, None
- Available data events: S3 object-level APIs, Lambda invoke function APIs
- Storage 
  - S3 bucket (required) 
    - SSE-S3 encryption by default, SSE-KMS alternatively
    - Can be in another account (need correct bucket policy)
  - CloudWatch Logs (optional) 
- Send message to SNS topic when log file has been delivered
- Not real-time delivery (about 15 minutes delay)

## Log File Integrity Validation
```sh
aws cloudtrail validate-logs
```

# CloudWatch

## CloudWatch Metrics
- EC2: CPU Utilization, Disk Reads/Writes (Instance store), Network In/Out, Status Check, CPU Credit
- EBS: Read/Write - Bandwidth, Throughput, Average Size, Average Latency; Average Queue Length, Time Spent Idle, Burst Balance
  - Not exist: Space used/left
- ASG: Configurations, Instance number in each status
  - You can enable Group Metrics Collection to aggregate EC2 instance metrics in the ASG
- ELB: Target Response Time, Requests, Target/HTTP Error Counts, Active/New Connection Count
- RDS: Exist - Free storage, RAM Usage, etc.

## Metrics Retention
- Less than 1 minute: 3 hours
- 1 minute: 15 days
- 5 minutes: 63 days
- 1 hour: 15 months

## Custom Metrics
- You can upload a single metric or a metric set

## Export Metrics
```sh
aws cloudwatch get-metric-statistics
```
- You can use Lambda function to perform scheduled export

## CloudWatch Alarms
- Threshold type: Static / Anomaly detection
- Actions: SNS, Auto Scaling action, EC2 action
- CloudWatch Alarms is not a event source of CloudWatch Events

## Billing Metrics & Alarms
- Only available in us-east-1 region

## Unified CloudWatch Agent
- Send logs from log files to CloudWatch Logs
- Send custom metrics to CloudWatch Metrics
- Agent configuration can be stored into SSM Parameter Store

## Application Logs Integrated with CloudWatch Logs
- Lambda
- ECS
- Elastic Beanstalk

## CloudWatch Events - API Call via CloudTrail
- Use API calls as event sources
  - Except for read-only APIs such as List, Get, Describe

## CloudWatch Events for S3 vs. S3 Events
- CloudWatch Events for S3
  - Bucket Level Operations
  - Object Level Operations: must enable object-level API tracking in CloudTrail for the target S3 buckets
- S3 Events
  - Object Level Operations only

## CloudWatch Dashboards
- Correlate multiple metrics in one view

## Other Features
- CloudWatch Logs - Metric Filters
- CloudWatch Logs - Subscriptions

# X-Ray

## Use CloudWatch Events to send events when X-Ray Detects Latency & Errors
- Create a schedule CloudWatch Events rule to trigger a Lambda function
- Calls GetServiceGraph API in the Lambda function
