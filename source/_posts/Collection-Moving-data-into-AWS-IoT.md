---
title: 'Collection: Moving data into AWS - IoT'
date: 2019-08-10 16:39:41
tags: Big Data
categories: AWS
---

- [Introduction](#Introduction)
- [AWS IoT](#AWS-IoT)
  - [Device Gateway](#Device-Gateway)
  - [Message Broker](#Message-Broker)
    - [Rules Engine](#Rules-Engine)
  - [Thing Registry](#Thing-Registry)
    - [Authentication](#Authentication)
    - [Authentication Policies](#Authentication-Policies)
  - [Device Shadow](#Device-Shadow)
  - [Greengrass](#Greengrass)

# Introduction

- Real Time - Immediate actions
  - Kinesis Data Streams
  - SQS
  - **IoT**
- Near Real Time - Reactive actions
  - Kinesis Data Firehose
  - DMS
- Batch - Historical Analysis
  - Snowball
  - Data Pipeline

# AWS IoT

## Device Gateway
- The entry point for IoT devices connecting to AWS IoT
- Supports the MQTT, WebSockets, and HTTP 1.1 protocols

## Message Broker
- For devices to communicate with others
- Pub/sub messaging pattern with low latency
- Supports the MQTT, WebSockets, and HTTP 1.1 protocols

### Rules Engine
- Rules are defined on the Broker topics
- Rule-action pairs
- Used to augment or filter data received from devices
- Rules need IAM Roles to perform the actions

## Thing Registry
- Represent all connected devices
  - A unique ID
  - Metadata
  - X.509 certificate
  - IoT Groups
- Can organizes the resources associated with each device

### Authentication
- Things
  - X.509 certificates
  - AWS SigV4
  - Custom tokens with custom authorizers
- Mobile apps
  - Cognito
- Web/Desktop/CLI
  - IAM
  - Federated Identities

### Authentication Policies
- IoT policies
  - Attached to X.509 certificates or Cognito Identities
  - Can be attached to Groups in stead of Things
- IAM policies
  - Used for controlling IoT AWS APIs

## Device Shadow
- Record the state of Things and synchronize the state when Things back up online
- JSON document

## Greengrass
- Brings the compute layer to the devices locally
- Execute AWS Lambda on the devices and operate offline
- Deploy functions from the cloud