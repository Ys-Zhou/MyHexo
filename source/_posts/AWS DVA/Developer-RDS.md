---
title: 'Developer: RDS'
date: 2022-02-19 19:59:37
tags: AWS - Developer
---

# RDS

## Backups

- Automatically enabled
- Daily full backup of the database
- Transaction logs every 5 minutes
- 7 days retention by default, up to 35 days

## Snapshots

- Manually triggered by uses

## RDS Storage Auto Scaling

- Automatically scaling the database storage
- Users need set maximum storage threshold and scaling policies
- Support all database engines

## Read Replicas

- Up to 5 Read Replicas for a RDS instance
- Network cost
  - **Free** for cross-**AZ** replication
  - **Charged** for cross-**region** replication

## Enforce SSL
- PostgreSQL: Set `rds.force_ssl=1` in RDS Parameter Groups
- MySQL: Run `GRANT USAGE ON *.* TO 'mysqluser'@'%' REQUIRE SSL;` SQL

# Aurora

- Storage automatically grows in increments of 10 GB, up to 64 TB
- Writer Endpoint & Reader Endpoint
- Up to 15 Read Replicas with auto-scaling feature
