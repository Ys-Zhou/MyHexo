---
title: 'Services: Others'
date: 2021-02-06 23:02:18
tags: 'AWS - Solutions Architect (Services)'
---

# CICD

- Code repository: AWS CodeCommit
- Build / Test: AWS CodeBuild
- Deploy: AWS CodeDeploy
- Pipeline: AWS CodePipeline

# AWS CloudSearch

- AWS managed ElasticSearch

# Alexa for Business

# Amazon Lex

- Automatic Speech Recognition (ASR) to convert speech to text
- Natural Language understanding to recognize the intent of texts
- Helps build chat chatbots

# Amazon Connect

- Cloud-based virtual contact center can receive call, create contact flows
- Can integrate with other CRM systems or AWS

# AWS Rekognition

- Recognize objects in images or videos using ML
- Can integrate with Lambda

# Kinesis Video Streams

- Video streams from streaming device or Kinesis Video Streams Producer library
- Cannot output the stream data to S3 (need build custom solution)
- Consumers
  - Kinesis Video Streams Parser Library
  - AWS Rekognition

# AWS WorkSpaces

- Cloud VDI (Virtual Desktop Infrastructure)
- Windows or Linux Desktop
- On demand
- Integrated with Microsoft AD

## WorkSpaces Application Manager

- Deploy and Manage applications as virtualized application containers
- Keep applications updated using WAM

## Windows Updates

- Windows Updates is default on
- You can define the Maintenance Windows to install updates
  - Always On: once a week
  - AutoStop: once a month
  - Manual maintenance: you define

# Amazon AppStream 2.0

- Desktop Application Streaming Service
- The application is delivered from within a web browser

# Amazon Mechanical Turk

- Crowd sourcing marketplace to perform human tasks
- Integrates with SWF, does not integrate with Step Functions

# AWS Device Farm

- Application testing service for your mobile and web applications
- Test across real browsers and real mobiles devices
- Fully automated using framework
- Generates videos and logs to document the issues encountered
- Can remotely log-in to devices for debugging
