---
title: 'Services: Service Communication'
date: 2020-08-03 22:03:05
tags: 'AWS - Solutions Architect (Services)'
---

# AWS Step Function

- Serverless visual workflow
- Represent flow as a JSON state machine
- Integrate with Lambda, EC2, API Gateway, CLI/SDK
- One year maximum execution time
- Possible to implement human approval feature

# Step Function Tasks

- Activity Tasks
  - Activity worker (HTTP)
  - Workers pull the step functions
- Service Tasks
  - Lambda function, ECS task, Fargate, DynamoDB, Batch job, SNS topic, SQS queue
- Wait Tasks
  - Wait for a duration or until a timestamp

# SWF

- Runs on EC2 (not serverless)
- One year maximum runtime
- Step Function is recommended to use, except
  - Need external signals to intervene in the processes
  - Need child processes that return values to parent process
  - Need to use Amazon Mechanical Turk

# SQS

- Max message size is 256 KB, use S3 for large messages
- Can be read from EC2, Lambda, etc.
- SQS FIFO
  - 300 messages/s without batching
  - 3000 messages/s with batching
- Messages can be processed more than once by consumer
  - Consumer has to be idempotent
- Use DLQ or Destination for failures

## Lambda Event Source Mapping

- Event Source Mapping will pull messages from SQS
- Batch size is 1-10
- It is recommended to set the queue visibility timeout of your Lambda function

# Amazon MQ

- Managed Apache ActiveMQ
- Amazon MQ runs on a dedicated machine
- Supported Protocols
  - MQTT, AMQP, STOMP, Openwire, WSS
- IBM MQ, TIBCO EMS, Rabbit MQ, and Apache ActiveMQ can be migrated to Amazon MQ
- If you do not want to change your application to use SQS or SNS, you can use Amazon MQ

# SNS

- Pub / sub
- Subscribers can be
  - SQS, HTTP(S), Lambda, Emails, SMS, Mobile Notifications

## SNS + SQS: Send Messages to Many Targets

- Push one message in SNS, receive in many SQS queues
