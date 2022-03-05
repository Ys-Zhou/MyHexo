---
title: 'Developer: Lambda'
date: 2022-02-14 14:47:16
tags: AWS - Developer
---

# ALB Multi-Value Headers

- By enabling multi-value headers, values share the same key in HTTP headers and query parameters will be send as an array in AWS Lambda event

```
http://example.com?key=value1&key=value2
```

```json
"multiValueQueryStringParameters": {"key": ["value1", "value2"]}
```

# Invocation Types

- Synchronous invocation
  - API Gateway, ALB
- Asynchronous invocation
  - S3, SNS, EventBridge

# Event Source Mapping

- When triggered from sources that records need to be polled
  - SQS & SQS FIFO queue
  - Kinesis Data Streams
  - DynamoDB Streams
- Synchronous invocation

## For Kinesis & DynamoDB

- Use batch windows to accumulate records before processing
- You can process multiple batches in parallel
  - Up to 10 batches per shard
  - One partition key per batch
  - Processions within a batch are in-order

## For SQS

- Lambda uses long polling to retrieve records
- Number of batches is up to 10
- For SQS FIFO, concurrency is up to the number of active message groups to keep processing in order
- It is recommended that set the visibility time out to 6x the function timeout
- Set DLQ on the SQS side (Event Source Mapping uses synchronous invocation)
- Or you can use lambda destination for failures

## Error Handling

- Default
  - Lambda will keep retrying the entire batch until the items expire
  - Then the precessing for the affected shard will be paused
- Configuration
  - Discard old events
  - Restrict retry times
  - Split the batch on error (used for resolving timeout)
- You can set Destination for discarded events

# Destinations

- Asynchronous invocations
  - Set destinations for successful and failed events to
    - SQS, SNS, Lambda, EventBridge
  - It is recommended to use destinations in stead of DLQ of Lambda
- Event source mapping
  - Set destinations for discarded event batch to
    - SQS, SNS
  - You can use DLQ of SQS instead

# Tracing with X-Ray

- Usage
  - Enable in Lambda configuration
  - Use X-Ray SDK in code
  - Attach `AWSXRayDaemonWriteAccess` policy to execution role
- Environment variables
  - `_X_AMZN_TRACE_ID`: tracing header
  - `AWS_XRAY_CONTEXT_MISSING`: LOG_ERROR by default
  - `AWS_XRAY_CONTEXT_ADDRESS`: X-Ray daemon IP address and port

# Lambda in VPC

- Lambda functions have no access to Internet if you place them in a public subnet
- You should place Lambda functions in a private subnet and attach a NAT Gateway / Instance

# Performance

## Put you common initial codes outside handler

```py
# bad
def lambda_handler(event, context):
  connect_db()
  do_something()
```

```py
# good
connect_db()
def lambda_handler(event, context):
  do_something()
```

## Use /tmp directory as temporary directory

- Max size is 512 MB
- The directory remains when the execution context is frozen, providing transient cache that can be used for multiple invocations

# Define Lambda Function in CloudFormation

## Inline

```yml
Resources:
  myLambdaFunction:
    Type: AWS::Lambda::Function
    Properties:
      Runtime: python3.x
      Handler: index.handler
      Code:
        ZipFile: |
          def handler(event, context):
            return 'OK!'
```

## S3

```yml
Resources:
  myLambdaFunction:
    Type: AWS::Lambda::Function
    Properties:
      Runtime: python3.x
      Handler: index.handler
      Code:
        S3Bucket: myBucket
        S3Key: myLambdaFunction.zip
        S3ObjectVersion: myVersionString
```

# Layers

- Custom Runtime
- Externalize Dependencies
