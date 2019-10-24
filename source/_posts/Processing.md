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
- Zeppelin / EMR Notebook
- Hive / Pig (old) / Presto
- MapReduce (old) / Spark / Tez
- YARN
- HDFS -> HBase

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

### Hive
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

#### Hive - AWS Integration
- Load data from S3 with table partitions
- Directly write data (tables) to S3
- Load scripts from S3
- Use DynamoDB as an external table

### Apache Pig

#### Pig Latin
- A scripting language
- Use SQL-like syntax to define your map & reduce steps
- Highly extensible with user-defined functions (UDF)

#### Pig - AWS Integration
- Query data in EMRFS (S3)
- Load JARs or scripts from S3

### HBase
- Non-relational, petabyte-scale database
- On the top of HDFS
- Operate largely in memory across the entire cluster
- Integration with Hive

#### HBase vs. DynamoDB
- HBase
  - Efficient with sparse data
  - Appropriate for high consistent reads & writes
  - Higher write & update throughput
  - More integration with Hadoop
- DynamoDB
  - Fully managed, auto-scaling
  - More integration with other AWS services (like Glue)

#### HBase - AWS Integration
- Can build on the top of EMRFS (S3)
- Can back up tp S3

### Presto
- Issue SQL style query
- Can connect to different *big data* databases / data stores at once
- Can issue interactive queries at petabyte scale
- Optimized for OLAP (also not for OLTP)
- Amazon Athena uses Presto under the hood
- Can be used through JDBC, CLI, Tableau interfaces

#### Presto Datasource
- HDFS
- S3 / EMRFS
- Cassandra
- MongoDB
- HBase
- RDBs
- Redshift
- Teradata

### Zeppelin
- Notebook
  - Can interactively run code against data
  - Can interleave with nicely formatted notes
  - Can share notebooks with others on the cluster
- Integration
  - Spark
  - Python
  - JDBC
  - HBase
  - Elasticsearch
  - etc.

#### Zeppelin with Spark
- Zeppelin can run Spark code interactively
  - Speed up your development cycle
  - Easier experimentation and exploration of your big data
- Can execute SQL queries using SparkSQL
- Query results can be visualized in charts and graphs

### EMR Notebook
- Notebook
  - More AWS integration than Zeppelin
- Automatically back up to S3
- Can provision or shut down clusters directly from notebook
- Hosted inside a VPC and can be accessed via AWS console
