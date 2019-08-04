---
title: 'Collection: Moving data into AWS - SQS'
date: 2019-08-04 15:58:18
tags: Big Data
categories: AWS
---

- [Introduction](#Introduction)
- [AWS SQS](#AWS-SQS)
  - [SQS - Standard Queue](#SQS-Standard-Queue)
  - [SQS - FIFO Queue](#SQS-FIFO-Queue)
  - [Producing Messages](#Producing-Messages)
  - [Consuming Messages](#Consuming-Messages)
  - [SQS Extended Client](#SQS-Extended-Client)
  - [Use Cases](#Use-Cases)
  - [Pricing](#Pricing)
  - [Security](#Security)

# Introduction

- Real Time - Immediate actions
  - Kinesis Data Streams
  - **SQS**
  - IoT
- Near Real Time - Reactive actions
  - Kinesis Data Firehose
  - DMS
- Batch - Historical Analysis
  - Snowball
  - Data Pipeline

# AWS SQS

## SQS - Standard Queue
- Auto Scales without limit
- Retention of messages from 1 minute to 14 days, 4 days by default
- the number of messages in the queue has no limit
- Low latency (~10 ms on publish and receive)
- Consumers can be scaled horizontally
- **Can have duplicate messages**
- **Can have out of order messages**
- **Limitation of 256KB per message**

## SQS - FIFO Queue
- Name of the queue ends in .fifo
- Up to 3,000 messages/s with batching or 300 messages/s without (soft limit)
- **Messages are sent only once**
- **Messages are processed in order**

## Producing Messages
- Message body
  - String
  - Up to 256KB
- Message attributes (metadata)
  - Key-value pair
- Delivery delay
- Return
  - Message ID
  - MD% hash of the body

## Consuming Messages
- Poll messages from SQS 
  - Up to 10 messages at a time
- Process the message within the visibility timeout
- Delete the message by message ID & receipt handle
- Maximum of 120,000 messages being processed by consumers

## SQS Extended Client
- Send messages larger than 256KB
- Not recommended
- Java Library
  - Send message to S3 first
  - Only send metadata to SQS

## Use Cases
- Decouple applications asynchronously
- Buffer writes
- Handle large loads of messages

## Pricing
- Pay per API request
- Pay network usage

## Security
- HTTPS endpoints
- Server Side Encryption using KMS
  - Only encrypts the body
- IAM
- SQS queue access policy