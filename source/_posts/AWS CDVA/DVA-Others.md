---
title: DVA - Others
date: 2022-01-29 23:37:30
tags: AWS - Developer
---

# IAM

## Policy Types

- AWS Managed Policies
- Customer Managed Policies
- Inline Policies

## Policy Structure

- Version: string
- Id: string
- Statement: array
  - Sid: string
  - Effect: "Allow" | "Deny"
  - Principal: object
  - Action: string | array
  - Resource: string | array
  - Condition: object

## Security Tools

- IAM Credentials Reports
  - Account-level report
  - All users in an account and their credentials
- IAM Access Advisor
  - User-level report
  - All service permissions and when they are used
  - Use this tool to review user privileges (least privilege principle)

# EBS Volume Types

- gp2: General purpose SSD
  - 1 GiB ~ 16 TiB
  - Can burst to 3000 IOPS for small size volumes
  - 3 IOPS per GB, up to 16000 IOPS
- gp3: General purpose SSD
  - 1 GiB ~ 16 TiB
  - Baseline of 3000 IOPS and throughput of 125 MiB/s
  - Up to 16000 IOPS and 1000 MiB/s
  - Provisioned IOPS and throughput
- io1 / io2: High-performance SSD
  - 4 GiB ~ 16 TiB
  - Up to 64000 Provisioned IOPS for Nitro EC2 instances
  - Up to 32000 Provisioned IOPS for other EC2 instances
  - io2 have more durability and more IOPS per GiB at the same price as io1 (io2 is always better than io1)
- io2 Block Express
  - 4 GiB ~ 64 TiB
  - Up to 256000 Provisioned IOPS
  - Sub-millisecond latency
- st1: Throughput optimized HDD
  - 125 MiB ~ 16 TiB
  - Up to 500 IOPS and 500 MiB/s throughput
- sc1: Cold HDD
  - 125 MiB ~ 16 TiB
  - Up to 250 IOPS and 250 MiB/s throughput

## Other key points

- Only gp2/3 and io1/2 (only SSD) can be used as boot volumes
- Only io1/2 supports EBS multi-attach

# ELB

## CLB

- Fixed hostname

## ALB

- Support HTTP(S), HTTP/2, WebSocket
- Support redirects (Ex. from HTTP to HTTPS)
- Support routing to different target groups based on path, hostname, query string, headers
- Supported target groups:
  - EC2 instances
  - ECS tasks
  - Lambda functions
  - Private IP addresses
- Fixed hostname
- Client info in headers
  - IP: X-Forwarded-For
  - Port: X-Forwarded-Port
  - Protocol: X-Forwarded-Proto

## NLB

- Supported target groups:
  - EC2 instances
  - Private IP addresses
  - ALB
- One static IP per AZ (fixed IPs)

## Gateway Load Balancer

- Layer 3 (Network Layer) - IP Packets load balancer
- Combines:
  - Transparent Network Gateway
  - Load Balancer
- Uses GENEVE protocol on port 6081
- Target groups
  - EC2 instances
  - Private IP addresses

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

# Route 53

- Authoritative DNS Service & Domain Registrar
  - Authoritative: customers can update the DNS records

## CNAME & Alias

- CNAME
  - hostname -> hostname
  - Can not be used for root domain (Zone Apex)
- Alias
  - hostname -> AWS resource hostname (amazonaws.com)
  - free
  - Native health check
  - Records Targets: ELB, CloudFront Distributions, API Gateway, Elastic Beanstalk environments, S3 Websites, VPC Interface Endpoints, Global Accelerator accelerators, Route 53 records in the same hosted zone
    - **Not include EC2 DNS name**

## Health Checks

- Health Checks are only for public resources
  - Public endpoint
  - Other health checks (calculated health checks)
  - CloudWatch Alarms

## Traffic Flow

- A visual tool to create complex route policies
- Use this tool to create Geoproximity Policy
  - Set bias

# AWS CLI & SDK

- Simulate API calls
  - Use Policy Simulator
  - Run CLI commends with `--dry-run`
- Decode authorization message when your API call failed
  - Run `sts decode-authorization-message` in CLI
- EC2 instance metadata
  - Request `http://169.254.169.254/latest/meta-data`
  - Ex. IAM Role name (cannot retrieve IAM Policies)
  - Ex. IAM security credentials
- CLI & SDK with MFA
  - Call `STS GetSessionToken` API
- SDK
  - CLI uses the Python SDK (boto3)
  - Default region is `us-east-1` if you do not specify one
- Exponential Backoff
  - Implement Exponential Backoff in your API calls to retry 5xx http errors
  - Better than Linear Backoff
  - SDK has already implemented this
- Credentials Provider Chain
  - CLI
    - Command line options
    - Environment variables
    - Credentials file (~/.aws/credentials)
    - Configuration file (~/.aws/config)
    - Container credentials (for ECS tasks)
    - Instance profile credentials (for EC2 instances)
  - SDK
    - Java JVM properties
    - Environment variables
    - The default credentials profile (in ~/.aws/credentials)
    - Container credentials (for ECS tasks)
    - Instance profile credentials (for EC2 instances)
- Signature v4 Signing
  - AWS use SigV4 to sign your credentials in your API calls
  - In header option or http query parameters
