---
title: 'Developer: Step Function'
date: 2022-02-19 19:51:56
tags: AWS - Developer
---

# Task States

- Invoke a service
  - Invoke a Lambda function
  - Run a AWS Batch job
  - Run a ECS task
  - Insert an item to DynamoDB
  - Publish a message to SNS or SQS
  - Launch another Step Function workflow
  - etc.
- Run an activity running in
  - EC2
  - ECS
  - On-premises

# States

- Choice State
- Fail / Succeed State
- Pass State
- Wait State
- Map State
- Parallel State

# Error handling
- Retry
  - ErrorEquals
  - IntervalSeconds
  - BackoffRate
  - maxAttempts
- Catch (if all retries fails)
  - ErrorEquals
  - Next
  - ResultPath: the path that error put
- Predefined error codes
  - States.ALL
  - States.Timeout
  - States.taskFailed
  - States.Permissions

# Workflow Types

||Standard|Express|
|-|-|-|
|Maximum duration|1 year|5 minutes|
|Start rate|Over 2000 per second|Over 100000 per second
|State transition rate|Over 4000 per second|Nearly unlimited|
|Pricing|More expensive (different pricing mode)|Cheaper (different pricing mode)|
