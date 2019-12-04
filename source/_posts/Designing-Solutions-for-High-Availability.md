---
title: Designing Solutions for High Availability
date: 2019-12-04 08:52:00
tags: AWS - Solutions Architect
---

# Building Fault Tolerance Applications

## AMI

- Being able to quickly launch replacement instances based on AMI is a critical first step towards fault tolerance
- AMI can able you to quickly recover from failures
  - If an instance fails, you can simply launch another one based on the same template
- To minimize downtime, your might event always keep a spare instance based on the same AMI
  - This can be down efficiently using EIP
  - You can easily replacement instance by remapping your EIP to the new instance

## EBS

- Use **snapshots** to backup EBS volumes
  - Snapshots can be used to create new EBS volumes at the time the snapshot was taken
  - Snapshots only represent the on-disk state of the application, you must pause I/O and flush in-memory data to disk before initiating a snapshot

- Fault tolerance of EBS
  - EBS volumes store data redundantly, making them more durable than a typical hard drive
  - A backup strategy includes an interval, a retention period, and a recovery plan
    - Interval: time between backups
    - Retention period: time to keep the snapshots
    - Recovery plan: procedures to restore the snapshots
  - Snapshots are stored in S3
  - EBS snapshots can be copied into other regions to create EBS volumes

## Auto Scaling

- Use Auto Scaling with CloudWatch to control termination and launching new EC2 instances
- Auto Scaling will automatically detect failures through health check and launch replacement instances
- It is recommended to using Auto Scaling to regularly turn your instances over
  - In order to avoid any possible memory leaks or degradation
  - Set expiry dates on your instances to ensure they remain fresh

## ELB
todo
