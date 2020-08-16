---
title: 'Services: Cost Control'
date: 2020-08-16 16:39:58
tags:
---

# Cost Allocation Tags

- Group resources in Billing Reports
- Tags take up 24 hours to show up in the report
- AWS Generated Tags
  - Automatically applied to resources
  - Tag prefix: aws:
  - They are not applied to resources created before enabling Allocation Tags
- User Tags
  - Tag prefix: user:

# Trusted Advisor

- Analyze AWS accounts and provides recommendation
  - Cost Optimization
  - Performance
  - Security
  - Fault Tolerance
  - Service Limits
- Best Practice Checks
  - Core checks only
    - Basic user & Developer
  - Full
    - Business & Enterprise
    - Programmatic Access using AWS Support API
- Can enable weekly email notification from the console

## Trusted Advisor Limits

- Trusted Advisor can check if a bucket is public, but it cannot check if an object is public
  - Use CloudWatch Events or S3 Events instead
- Trusted Advisor can monitor service limits, but it cannot change the limits
  - Manually create a ticket in Support Center or use Service Quotas to change limits Programmatically

# EC2 Cost Optimization

## EC2 Instance Launch Types

- On-Demand Instances
- Spot Instances
- Reserved Instances
  - Reserved Instances
  - Convertible Reserved Instances
  - Scheduled Reserved Instances
- Dedicated Instances
- Dedicated Hosts

## AWS Savings Plan

- New pricing model to get a discount based on long-term usage
- Any usage beyond the savings plan is billed at the on-demand price
- EC2 Instance Savings plan 
  - Up to 72% off, same as Standard RIs
  - Fixed: instance family, region
  - Flexible: instance size, OS, tenancy
- Compute Savings plan
  - Up to 66% off, same as Convertible RIs
  - All flexible: instance family & size, region, OS, tenancy

# S3 Cost Optimization

## S3 Tiers

- S3 Standard
- S3 Standard-IA
- S3 One Zone-IA
- S3 Intelligent Tiering
- S3 Glacier
- S3 Glacier Deep Archive

## Glacier & Glacier Deep Archive

- Retrieval options
  - Glacier
    - Expedited: 1 - 5 minutes
    - Standard: 3 - 5 hours
    - Bulk: 5 - 12 hours
  - Glacier Deep Archive
    - Standard: 12 hours
    - Bulk: 48 hours
- Minimum storage duration
  - Glacier: 90 days
  - Glacier Deep Archive: 180 days

## S3 Cost Saving

- Use S3 & Glacier Select
- Use S3 Lifecycle Rules
- Compress objects
- S3 Requester Pays
  - Requesters pay the data transfer cost
  - Bucket owner still pays the storing cost
  - If the user assume a IAM role, the requester is the owner of the role
