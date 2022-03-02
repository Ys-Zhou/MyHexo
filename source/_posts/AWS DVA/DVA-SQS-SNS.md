---
title: DVA - SQS & SNS
date: 2022-02-12 19:58:27
tags: AWS - Developer
---

# SQS

## Access Policy

- Cross-account access
- Allow S3 Events to send message
  - Neither has service-linked roles
  - Both have resource-based policies (access policies)

## Settings

- Message Visibility Timeout: The invisibility time to other consumers after the message is polled by any consumer
  - The consumer can call `ChangeMessageVisibility` API to change the visibility timeout for a specific message
- Dead Letter Queue
- Delay Queue
  - Queue level delay
  - Message level delay: set `DelaySeconds` parameter
- Polling Wait Time
  - 1 ~ 20 second
  - Long polling is better than short polling (reduce API calls, better latency)
  - Queue level or API call level (use `ReceiveMessageWaitTimeSeconds` parameter)

## Extended Client

- A pre-build SDK for application to send message larger than 256KB
- Use S3 under the hood

## Important APIs

- CreateQueue
- DeleteQueue
- PurgeQueue: delete all the messages in the queue
- SendMessage (has Batch API)
- ReceiveMessage (`MaxNumberOfMessages` & `ReceiveMessageWaitTimeSeconds` parameters)
- DeleteMessage (has Batch API)
- ChangeMessageVisibility (has Batch API)

## FIFO De-duplication

- De-duplication interval is 5 minutes
- De-duplication methods
  - Compare messages by SHA-256 hash
  - Using Message De-duplication ID

## FIFO Message Grouping

- Default FIFO queue only has one message group
- Messages in a message group are in order and only consumed by one consumer
- You can add message groups to consume message in parallel
- The message order between message groups is not guaranteed

# SNS

## Message Filtering

- Filter messages on different subscribers