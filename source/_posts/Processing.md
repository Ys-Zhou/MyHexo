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

- Discovery and definition of table definitions and schema form
  - Redshift
  - S3
  - RDS
  - Other RDBs
- ETL jobs by
  - Trigger-driven
  - On a schedule
  - On demand

## Glue Crawler
- Scans data in S3 and populate Glue Data Catalog
  - Infer the schema automatically

## Glue Data catalog
- Central metadata repository
  - Just store the data definition and do not remove the data itself
  - Can used by Athena, Redshift, EMR, QuickSight, ect.
- S3 Partitions
  - Glue crawler will extract partitions based on how S3 data is organized
  - Organize the data structure based on your query priority
- EMR Hive
  - Glue Data Catalog can provide metadata information to Hive
  - You import a Hive meta store into glue

## Glue ETL
- Automatic code generation
  - Scala or Python
- Encryption at rest and in transit
- Can be event-driven
- Can provision additional Data Processing Units (DPU) to increase performance
- Monitor through CloudWatch

## Anti-patterns
- Streaming data
  - Glue is batch oriented, has minimum 5 minute intervals
  - Use Kinesis and store data in S3 or Redshift in temporary
- Using other ETL engines than Spark
  - Glue ETL is implemented in Spark
  - Use Data Pipeline or EMR
- NoSQL databases
  - Schema makes no sense for NoSQL databases

# Amazon EMR


