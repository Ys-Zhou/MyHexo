---
title: 'Summary: Caching'
date: 2020-08-02 10:37:49
tags: 'AWS - Solutions Architect (Summary)'
---

# CloudFront

## CloudFront Origins

- S3
  - Must define Origin Access Identity and S3 bucket policies
  - If you define OAI, only CloudFront can access the S3 buckets
  - Traffics between Edge Locations and S3 buckets go over private network
- Custom origin
  - ALB, EC2, etc.
  - Traffics between Edge Locations and backends go over public Internet
  - Your backends must be public
- Support primary-secondary origins

### S3 with CloudFront vs. S3 Cross-Region Replication

- S3 with CloudFront
  - Files are cached
  - For static contents that must be available everywhere
- S3 Cross-Region Replication
  - Readonly
  - For dynamic contents that need to be low-latency in a few regions

## CloudFront Geo Restriction

- Restrict by Geo
  - Whitelist
  - Blacklist

## CloudFront Signed URL & Signed Cookies

- Restrict the access to files
  - Signed URL = access to individual files
  - Signed Cookies access to multiple files
- Policies
  - URL expiration
  - IP ranges
  - Trusted signers (AWS accounts)

### CloudFront Signed URL vs. S3 Pre-Signed URL

- CloudFront Signed URL
  - Multiple origins
  - Can filter by IP, path, date
  - Cache files
- S3 Pre-Signed URL
  - Only S3
  - Anyone who use Pre-Signed URL will be assumed as having an IAM role
  - Directly GET from / PUT to S3 buckets

## CloudFront Caching

- Can based on
  - Headers
  - Session Cookies
  - Query String
- TTL
  - 0 second to 1 year

### Whitelist Headers

- Caching based on whitelist items in headers
- Filter useless key-value pairs in request headers

### CloudFront Caching vs. API Gateway Caching

- API Gateway Edge
  - Use API Gateway Caching
    - Cache at region
- API Gateway Regional with CloudFront
  - Use API Gateway Caching
    - Cache at region
  - Use CloudFront Caching
    - Cache at edge

## Lambda@Edge

- Deploy Lambda functions at Edge Locations
- Used to modify CloudFront requests and responses
  - Viewer requests / responses
  - Origin requests / responses
- Use cases
  - User Authentication and Authorization
  - Website Security and Privacy
  - Dynamic Web Application at the Edge
  - Search Engine Optimization
  - Bot mitigation
  - Real-time Image Transformation
  - A/B Testing
  - User Prioritization, tracking, and analytics
  - Increasing cache hit ratio

## CloudFront with HTTPs

- CloudFront and Origin have separate SSL certificate
  - Hostname of CloudFront and Origin must be different
  - Do not use header forward (CloudFront will change **header Host value** to match origin automatically)
- CloudFront and Origin use the same SSL certificate (CloudFront hostname)
  - Hostname of CloudFront and Origin must be different
  - Use header forward
  - Use Lambda@Edge to set the correct **Hostname in URL**

# ElastiCache

- In-memory database
- Managed Redis or Memcached
- Using ElastiCache involves heavy application code changes
- Use cases
  - DB cache
  - User session store

## Redis vs. Memcached

- Redis
  - Multi-AZ with Auto-Failover
  - Can create Read Replicas
  - Data is persistent
  - Can backup and restore data
- Memcached
  - Multi-node for partitioning data
  - Not persistent
  - No backup and restore

# Architecture: Handling Extreme Rates

- Cache DNS records from Route 53 on client-side
- CloudFront is designed to handle Extreme Rates
  - 100000 RPS
  - Cache capability
- ALB can scale tremendously
  - Need warmup (take time to scale up)
- API Gateway
  - 10000 RPS (soft limit)
  - Cache capability
- Compute
  - EC2 with ASG, ECS can scale but slow
  - Fargate is faster
  - Lambda is up to 1000 concurrent (soft limit)
- Database
  - RDS and ElasticSearch is provisioned
  - DynamoDB has auto or on-demand scaling
    - DAX
- ElastiCache
  - Redis is up tp 200 nodes
  - memcached is up tp 20 nodes
- Store
  - EBS is up to 16k IPOS for gp2, 64k IOPS for io1
  - EC2 Instance Store is up to millions IOPS
  - EFS can use Max IO mode instead of General mode
  - S3
    - 3500 PUT, 5500 GET per prefix per second
    - If you use SSE-KMS, KMS may limit S3 performance
- Delivery
  - SQS and SNS is unlimited
  - SQS FIFO
    - 3000 RPS with batching
    - 300 RPS without batching
  - Kinesis need provisioning
    - 1 MB/s in per shard
    - 2 MB/s out per shard
