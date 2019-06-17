---
title: Basic AWS Best Practices
date: 2019-06-16 17:19:42
tags: AWS
categories: Cloud
---

## Three ways to deploy applications to the cloud
* Life and shift
* Cloud optimized
* Cloud native architecture

## Best Practices

### 1. Design for Failures
* Use multi-AZ
* Use ELB
* Use EIP
* Do real-time monitoring with CloudWatch
* Use SNS for real-time alarms based on CloudWatch metrics
* Create database slaves across AZs

### 2. Build Security in Every Layer
* Encrypt data in transit and at rest
* Use KMS
* Least privilege with IAM
* Use MFA
* Use Security groups and NACLs

### 3. Leverage Multiple Storage Options
* S3 (Large objects)
* Glacier (Achieve data)
* CloudFront (Content distribution)
* DynamoDB (Simple nonrelational data)
* EC2 Ephemeral Storage (Transient data)
* EBS (Persistent block storage with snapshots)
* RDS & Aurora
* Redshift (Data warehouse workloads)

### 4. Implement Elasticity
* Auto scaling
* ELB
* DynamoDB (maintain the user state information)

### 5. Think Parallel
* Use SNS adn SQS
* Use multithreading and concurrent requests to the cloud
* Run parallel MapReduce jobs (EMR)
* Use ELB to distribute load
* Use Kinesis for concurrent processing of data
* Use Lambda
* Use multipart upload adn ranged gets to upload files in S3

### 6. Loosely Couple Your Architecture

### 7. There Are No Constraints in the AWS Cloud