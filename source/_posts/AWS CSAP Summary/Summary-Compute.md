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
