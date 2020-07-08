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
