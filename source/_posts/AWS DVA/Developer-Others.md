---
title: 'Developer: Others'
date: 2022-01-29 23:37:30
tags: AWS - Developer
---

# AWS CLI & SDK

- Simulate API calls
  - Use Policy Simulator
  - Run CLI commends with `--dry-run`
- Decode authorization message when your API call failed
  - Run `sts decode-authorization-message` in CLI
- EC2 instance metadata
  - Request `http://169.254.169.254/latest/meta-data`
  - Ex. IAM Role name (cannot retrieve IAM Policies)
  - Ex. IAM security credentials
- CLI & SDK with MFA
  - Call `STS GetSessionToken` API
- SDK
  - CLI uses the Python SDK (boto3)
  - Default region is `us-east-1` if you do not specify one
- Exponential Backoff
  - Implement Exponential Backoff in your API calls to retry 5xx http errors
  - Better than Linear Backoff
  - SDK has already implemented this
- Credentials Provider Chain
  - CLI
    - Command line options
    - Environment variables
    - Credentials file (~/.aws/credentials)
    - Configuration file (~/.aws/config)
    - Container credentials (for ECS tasks)
    - Instance profile credentials (for EC2 instances)
  - SDK
    - Java JVM properties
    - Environment variables
    - The default credentials profile (in ~/.aws/credentials)
    - Container credentials (for ECS tasks)
    - Instance profile credentials (for EC2 instances)
- Signature v4 Signing
  - AWS use SigV4 to sign your credentials in your API calls
  - In header option or http query parameters

# EventBridge

- Event buses
  - Default event bus: handle AWS services events (CloudWatch Events)
  - Partner event bus: handle events from partners
  - Custom event buses: handle events form your own applications
- Event buses can be accessed cross-accounts
- Schema Registry
  - A tool to discover the structure of events

# CloudWatch Logs Encryption

- Associate a KMS CMK with a log group
- Can only be done through CLI
  - `associate-kms-key`
  - `create-log-group`

# CloudTrail Events

- Management Events
  - Default enabled
  - Write / Read Events
- Data Events
  - S3 and Lambda
  - Default disabled
  - Write / Read Events
- Insight Events
  - CloudTrail Insight will continuously analyzes Management Events and put detected unusual events to Insight Events

# Cloud Development Kit (CDK)

- Use programming languages to define IaS that can be translated to CloudFormation template

# AppSync

- Managed GraphQL
- Retrieve data in real-time with WebSocket or MQTT on WebSocket
  - Can be used to sync data
- Authorization
  - API_KEY
  - AWS_IAM
  - OPENID_CONNECT
  - AMAZON_COGNITO_USER_POOLS
- Can use CloudFront to distribute AppSync

# KMS APIs

- `Encrypt`: encrypt up to 4 KB of data
- `Decrypt`: decrypt up to 4 KB of data
- `GenerateDataKey`: return a data key with a encrypted copy of the data key
  - You can use cache feature to cache data key locally
- `GenerateDataKeyWithoutPlaintext`: only return a encrypted data key
  - Use `Decrypt` to get data key in plaintext
- `GenerateRandom`: return a random string

# SSM Parameter Store Parameter Policies

- Expiration
- ExpirationNotification
- NoChangeNotification

# Cloud Map

- AWS managed resource discovery service

# Fault Inject Simulator

- Chaos Engineering tools
- Make failures on AWS services
