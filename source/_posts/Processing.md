---
title: Processing
date: 2019-09-04 20:10:00
tags: AWS - Big Data
---

# AWS Lambda

- Running little stateless code often used to glue different services
  - Real-time file / stream processing
  - ETL
  - Cron job (use time as trigger)
  - Processing AWS events
- Supported languages
  - Node.js
  - Python
  - Java
  - C#
  - Go
  - Powershell
  - Ruby
- Many AWS services can trigger Lambda
  - S3
  - Kinesis (Lambda is **pulling** the data from Kinesis in batches)
  - DynamoDB
  - SNS
  - SQS
  - IoT
  - etc.
- Many AWS services can act as the destination
  - Elasticsearch
  - Data Pipeline
  - Redshift
  - etc.
- Can use DynamoDB or S3 to store stateful data
- Unlimited scalability (soft limit of 1000 concurrent)
- Max timeout can be set is 900 seconds

## Kinesis Data Streams + Lambda
  - Lambda can receive an event with a batch of stream records
    - Up to 10,000 records in a batch
    - Up to 6 MB in a function call (will be split automatically)
  - Lambda has a 900 seconds timeout limit
    - Lambda will keep retrying until succeeds or data expires
    - Retrying still uses shards
      - Avoid errors
      - Smaller batch size or longer timeout
      - Provision more shards
  - Lambda processes shard data synchronously
    - Shards will not be released when data is being processed by Lambda

## Anti-patterns
- Long-running applications
  - Max running time is 900s, use other services like Elastic Beanstalk
- Dynamic websites
  - Too much queries, use servers like EC2 instead
- Stateful applications
  - Lambda is stateless, use DynamoBD to store stateful data or use stateful services

# AWS Glue

- ETL service
