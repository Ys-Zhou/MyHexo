---
title: 'Summary: Compute'
date: 2020-07-08 00:09:23
tags: 'AWS - Solutions Architect'
---

# EC2

## Instances Types

- R (RAM): in-memory caches
- C (CPU): compute / databases
- M: general purpose / web app
- I (I/O): databases
- G (GPU): video rendering / machine learning
- T2 / T3 (burstable instances)

## Placement Groups

- Control EC2 instance placement strategy
- Group strategies:
  - Cluster: clusters instances into a low-latency group in a single AZ
  - Spread: spreads instances across underlying hardware (up to 7 instances per group per AZ)
  - Partition: spreads instances across partitions (up to 7 partitions & 100s of instances per group per AZ)
- Move an instance into or out of a placement group
  - Stop the instance
  - Modify the instance placement group
  - Start the instance

## Instances Launch Types

- On Demand Instances
- Spot Instances
- Reserved Instances
  - Reserved Instances
  - Convertible Reserved Instances
  - Scheduled Reserved Instances
- Dedicated Instances
- Dedicated Hosts
  - Book an entire physical server
  - Can define host affinity so that instance reboots are kept on the same host

### Spot Instances

- Define max spot price, and you will get the instance while current spot instances price are less the the price
- You can also buy a Spot Block, that the instances will not be interrupted during a specified time frame (1 to 6 hours)
- Spot Fleets
  - Fleets of Spot Instances and optionally On-Demand Instances
  - Can define max price for each Spot Instance
  - Can have a mix of instance types
  - Support for EC2, ASG, ECS with ASG, AWS Batch

## Metrics

- CPU Utilization
- Network In / Out
- Disk Read / Write for instance store
- Status Check
  - Instance status (VM)
  - System status (underlying hardware)
- **RAM is not a EC2 metric**, need to push it to CloudWatch as a custom metric

## Instance Recovery

- CloudWatch Alarm can monitor EC2 instances status check
- If any check failed, it can auto recovery the instance
- Preserve same private / public / Elastic IP, metadata, placement group

# Auto Scaling

## About Auto Scaling

- Spot Fleets support
- Update AMI
  - Update launch configuration / template
  - Terminate instances manually
  - You can use CloudFormation to automate the steps
- Scheduled scaling actions
  - Increase or decrease instances in a schedule
- Lifecycle Hooks
  - Perform actions before launch or terminate a instance

## Scaling Policies

- Simple Scaling (Step Scaling): increase or decrease instances based on 2 CloudWatch alarms
- Target Tracking: smart adjust instances to close to a metric

## Scaling Processes

- Launch
- Terminate
- HealthCheck
- ReplaceUnhealthy
- AZRebalance
- AlarmNotification: when accept notification from CloudWatch
- ScheduledActions
- AddToLoadBalancer
- You can suspend above processes

## Health Checks

- Check types
  - EC2 Status Checks
  - ELB Health Checks (HTTP)
- ASG will launch a new instance to replace the unhealthy one

## Architecture: Update instances in ASG

- Solution 1: Create new instances with the new template in the same ASG, and terminate old instances later
- Solution 2: Create a new ASG connected to the same load balancer, and split traffic between ASGs
- Solution 3: Create a new load balancer, use Route 53 CNAME weight record to split traffic (has risks because it's based on DNS)

# ECS

- Classic ECS: Running ECS on user-provisioned EC2 instances
- Fargate: Running ECS tasks on AWS managed compute (serverless)
- EKS: ECS for Kubernetes
- ECR: Docker Container Registry hosted by AWS

## ECS Concepts

- Cluster
- Service
- Tasks & task definition: containers running to create the applications

## ALB Port Mapping

- Allows you to run multiple instances of the same application on the same EC2 instance

## ECS Security & Networking

- IAM security
  - EC2 Instance Role must have basic ECS permissions
  - ECS Task level should have an IAM Task Role
- Integration with SSM Parameter Store & Secrets Manager
- Tasks networking
  - none: no network, no port mappings
  - bridge: use Docker's virtual network
  - host: use the underlying host network interface
  - awsvpc
    - A task has its own ENI and a private address
    - Enhanced security with Security Groups, monitoring, and VPC flow logs
    - Default mode for Fargate

## ECS Auto Scaling

- CPU and **RAM** is tracked in CloudWatch at ECS service level
- ESC Service Auto Scaling cannot auto scale EC2 instances
- Fargate Auto Scaling is easier to use (serverless) 

# AWS Lambda

## Lambda Runtime

- Node.js
- Python
- Ruby
- Java
- Golang
- C#
- Powershell

## Lambda Limits

- RAM: 128 MB to 3G
- Timeout: 15 minutes
- Storage: up to 512 MB
- Deployment package: up to 250 MB
- Concurrency: 1000 (soft limit)
- Latencies
  - Cold Invocation: ~100 ms
  - Warm Invocation: ~ms
  - Use Provisioned Concurrency to keep some functions warm
  - Use X-Ray to trace end-to-end latency

## Lambda Security

- IAM Roles
- Lambda Resource-based Policies

## Lambda in VPC

- Lambda can be deployed in VPC
- Connect internet from private subnet
  - Use NAT and IGW
  - Use Endpoints
- CloudWatch do not need NAT or Endpoint

## Lambda Logging

- CloudWatch
  - CloudWatch Logs
  - CloudWatch Metrics display Lambda metrics
  - Make sure Lambda has correct IAM role to write logs to CloudWatch Logs
- X-Ray
  - Trace Lambda
  - Need enable in Lambda configuration or use AWS SDK in Code

## Lambda Invocation Types

- Synchronous Invocations
  - Results is returned right away
  - Client need handle the errors
- Asynchronous Invocations
  - Lambda attempts to retry on errors (up to 3 times)
  - Make sure the processing is idempotent
  - Can define a DLQ (to SNS or SQS) for failed processing
- Event Source Mapping
  - Pull batches from stream sources
    - Kinesis Data Streams
    - SQS
    - DynamoDB Streams
  - If your function returns an error, the entire batch is reprocessed until success

## Lambda Destinations

- Can configure to send result to a destination
- In asynchronous invocations, can define destinations for successful and failed event to
  - SQS
  - SNS
  - Lambda
  - EventBridge bus
- In Event Source Mapping, can send discarded event batches to
  - SQS
  - SNS
- AWS recommends you use destinations instead of DLQ

## Lambda Versions & Aliases

- Versions
  - Versions have increasing version numbers
  - Versions are immutable, Any changes to a function will publish new versions
  - Latest version is marked as $LATEST, which is default version
  - Versions get their own ARN
- Aliases
  - An alias point to a version
  - Aliases are mutable and can be defined by user
  - Use aliases to do Blue / Green deployment
    - CodeDeploy can help to automate traffic shift
    - Linear: grow traffic every N minutes until 100%
    - Canary: try at x% then jump to 100%
    - AllAtOnce: immediate
  - Aliases get their own ARN
