---
title: 'Services: Monitoring'
date: 2020-08-08 21:07:36
tags: 'AWS - Solutions Architect (Services)'
---

# CloudWatch

## CloudWatch Metrics

- For EC2
 - 5 minutes or 1 minute bu using detailed monitoring
 - RAM is not a built-in metric
 - Custom metrics: 1 minute interval for standard resolution, 1 second interval for high resolution
- Many other AWS services

## CloudWatch Alarms

- Trigger actions
  - EC2 actions (reboot, stop, terminate, recover)
  - Auto Scaling actions
  - SNS
  - CloudWatch Events

## CloudWatch Events

- Events from AWS services
- Can also be triggered by CloudWatch Alarms and CloudTrail
- Events targets
  - Compute: Lambda, Batch, ECS tasks
  - Orchestration: Step Functions, CodePipeline, CodeBuild
  - Integration: SQS, SNS, Kinesis Data Streams, Kinesis Data Firehose
  - Maintenance: SSM, EC2 actions

## CloudWatch Logs

- Sources
  - SDK, CloudWatch Logs Agent, CloudWatch Unified Agent
  - Elastic Beanstalk
  - ESC
  - Lambda
  - VPC Flow Logs
  - API Gateway access logs
  - CloudTrail Logs based on filter
  - Route 53 DNS query logs
- Logs can be organized by Log Groups or Log Streams
- Can define log expiration policies
- Optional KMS encryption
- CloudWatch Logs can send logs to
  - S3
  - Kinesis Data Streams
  - Kinesis Data Firehose
  - Lambda

### Export logs to S3

- S3 buckets must be encrypted by SSE-S3, not SSE-KMS
- Log data can take up to 12 hours to become available for export
- Have to call *CreateExportTask* API manually
- For automation, use CloudWatch Logs Subscription Filter instead

### CloudWatch Logs Subscription Filter

- Destinations
  - Lambda
  - Kinesis Data Firehose
  - Kinesis Data Streams
- Destinations can be Kinesis Data Streams in other accounts & regions
  - Can be used to perform multi-account & multi-region log aggregation

### CloudWatch Logs Metric Filter

- CloudWatch Logs can use filter expressions
  - Metric filters can trigger CloudWatch Alarms

### CloudWatch Logs Insights

- Query logs and add queries to CloudWatch Dashboards

### CloudWatch Logs Agent vs. Unified Agent

- CloudWatch Logs Agent (old version)
  - Can only send log files
- CloudWatch Unified Agent
  - Can send log files and system-level metrics as logs
  - Centralized configuration using SSM Parameter Store
- Both can send logs using batching
  - batch_count (10000 by default, 1 at min)
  - batch_duration (default & min: 5000ms)
  - batch_size (default & max: 1 MB)

## CloudWatch Dashboards

- Display metrics and alarms
- Show metrics of multiple regions

# AWS X-Ray

- Visual analysis of services
- Tracing requests across services
- Integrations
  - EC2 & ECS: install X-Ray agent
  - Lambda
  - Elastic Beanstalk
  - API Gateway
