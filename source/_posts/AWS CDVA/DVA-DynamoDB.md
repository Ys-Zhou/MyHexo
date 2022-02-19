---
title: DVA-DynamoDB
date: 2022-02-16 15:17:30
tags: AWS - Developer
---

# WCU & RCU

- Provision 
  - ( `N` = number of items per second; `M` = item size in KB )
  - WCU for Standard Write: `N * ceil(M)`
  - RCU for Eventually Consistent Read: `(N / 2) * ceil(M / 4)`
  - RCU for Strongly Consistent Read: `N * ceil(M / 4)`
  - Transactional Write / Read costs 2 * WCU / RCU

- On-demand

# APIs

- `PutItem`
  - Create a new Item or replace an old item
- `UpdateItem`
  - Update attributes of an existing item or add a new item
  - Can be used for Atomic Counters
- `GetItem`
  - Read based on primary key (partition key or partition key + sort key)
  - `ProjectionExpression` can be specified to retrieve only certain attributes
- `Query`
  - `KeyConditionExpression`
    - Partition key value (only `=` expression)
    - Sort key expression
  - `FilterExpression`
    - Used on non-key attributes
  - `Limit`
    - Limit number of retrieved items
  - Up to 1 MB of data each query. Use pagination to recursively retrieve data
- `Scan`
  - Scan the entire table and then filter out data
  - Up to 1 MB of data each page
  - Can use `Limit`, `ProjectionExpression`, and `FilterExpression` 
- `ParallelScan`
- `DeleteItem`
  - Ability to perform a conditional delete
- `DeleteTable`
- Batch Operations
  - `BatchWriteItem`
    - Up to 25 `PutItem` & `DeleteItem` in one call
  - `BatchGetItem`
    - Return items from one or more tables
    - Up to 100 items and 16 MB of data

# Secondary Indexes

- LSI
  - Alternative Sort Key
  - Up to 5 LSI per table
  - Must be defined at table creation time
  - Attribute projections (attributes included in the LSI)
    - KEYS_ONLY
    - INCLUDE
    - ALL
- GSI
  - Alternative Primary Key
  - Can be added or modified after table creation
  - Must provision RCU and WCU for GSI
  - Attribute projections
  - **Write to a table will consumed WCUs of the table and all GSIs of the table**

# Optimistic Locking (Conditional Writes)

- Perform a write/update/delete only if conditions are met
  - In case that multiple uses update one table at the same time

# DAX

- Cache cluster in VPC
- Up to 10 nodes
- Multi-AZ

# Streams

- Triggers
  - Kinesis Data Streams
  - Lambda
  - KCL applications
- Data retention can be up to 24 hours
- Information in streams
  - KEY_ONLY
  - NEW_IMAGE
  - OLD_IMAGE
  - NEW_AND_OLD_IMAGE

# Copy Table

- Use Data Pipeline
  - Copy the data to S3 through a EMR cluster
  - Load the data from S3 through a EMR cluster
- Backup to / restore from S3 on yourself
