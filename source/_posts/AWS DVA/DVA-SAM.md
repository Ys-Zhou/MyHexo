---
title: DVA-SAM
date: 2022-02-17 23:13:34
tags: AWS - Developer
---

# Recipe

- Transform Header
```yml
Transform: 'AWS::Serverless'
```
- Resource Types
  - AWS::Serverless::Function
  - AWS::Api
  - AWS::SimpleTable

# Deployment

- `sam build`
  - Create local deployment artifacts
- `sam package`
  - Transform SAM templates to CloudFormation templates
  - Zip & upload codes to S3
- `sam deploy`
  - Create & execute CloudFormation Change Set

# Serverless Application Repository (SAR)

- Managed repository for serverless applications
- Can be share with specific AWS accounts or publicly
