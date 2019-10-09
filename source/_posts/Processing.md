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

- Elastic MapReduce
- Managed Hadoop framework on EC2 instances
- Tools
  - Spark
  - HBase
  - Presto
  - Flink
  - Hive
  - etc.
- EMR Notebooks
  - Query data interactively using Python
- Integration with other AWS services

## EMR Cluster
- Master node (leader node)
  - One master node in a cluster
  - Coordinate the distribution of data and tasks
- Core node
  - Host HDFS data and run tasks
  - Can be scaled up & down, but may lose data
    - Because core nodes store data themselves
- Task node
  - run tasks but do not host data
  - Good use of **spot instances**

## EMR Usage
- Cluster types
  - Transient cluster
  - Long-running cluster
- Run jobs
  - Connect directly to master to run jobs
  - Submit ordered steps via the console

## AWS Integration
- EC2 (different types)
- VPC
- S3
- CloudWatch
- IAM
- CloudTrail
- Data Pipeline (schedule and start clusters)

## EMR Storage
- HDFS
  - EBS
    - Can only be added when launching
    - Will be regarded as failure when detach an EBS volume at running
  - Ephemeral
  - Resilient and durable (data are span multiple instances)
  - Data block (chunk) size is 128 Mb
- EMRFS
  - S3
  - Store input and output data
  - Consistency
    - EMRFS Consistent View
    - Use DynamoDB to track consistency

## EMR Promises
- Provisions new nodes if core nodes fail
- Can add and remove tasks nodes on the fly
- Can resize running core nodes

## Hadoop
- Hive
- MapReduce / Spark / Tez
- YARN
- HDFS

### Spark
- Distributed processing system
- Use in memory caching
- Use directed acyclic graphs to optimize query execution
- Example use cases
  - Stream processing through Spark Streaming (from Amazon Kinesis / Apache Kafka / etc.)
  - Streaming analytics and write the results to HDFS or EMRFS (S3)
  - Machine learning using MLLib
  - Interactive SQL using Spark SQL
- Can not do real time job like OLTP

#### Spark structure
- Driver program / driver script
  - Spark Context
  - Job code
- Cluster Manager
  - Spark / YARN
  - AWS EMR uses YARN
- Executors
  - Cache
  - Tasks

#### Spark Components
- Spark Streaming
- Spark SQL
- MLLib
- GraphX

#### Spark Structured Streaming
- Spark Streaming will add new streaming data to the tail of exist data
- Can use library built on Kinesis Client Library (KCL) to handle data from Kinesis Data Streams
- Can do huge ETL on Redshift using Spark

### Apache Hive
- Run SQL code (HiveQL) on unstructured data that live in Hadoop, Yarn, or S3
- Easier and faster than writing MapReduce even Spark code for simple queries
- Highly optimized 
- Highly extensible
  - External applications can communicate with hive using JDBC or ODBC

#### Hive Metastore
- Structure definition on the unstructured data that is stored on HDFS, EMRFS, etc.
- Metastore is stored in MySQL on the master node by default
  - Ephemeral
- External Hive Metastore
  - AWS Glue Data Catalog
    - Can be used by Redshift, Athena, etc.
  - Amazon RDS

#### Hive Integration
- Load data from S3 with table partitions
- Directly write data (tables) to S3
- Load scripts from S3
- Use DynamoDB as an external table

### Apache Pig