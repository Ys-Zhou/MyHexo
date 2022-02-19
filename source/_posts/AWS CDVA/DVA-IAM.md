---
title: DVA - IAM
date: 2022-02-19 20:01:35
tags: AWS - Developer
---

# Policy Types

- AWS Managed Policies
- Customer Managed Policies
- Inline Policies

# Policy Structure

- Version: string
- Id: string
- Statement: array
  - Sid: string
  - Effect: "Allow" | "Deny"
  - Principal: object
  - Action: string | array
  - Resource: string | array
  - Condition: object

# Security Tools

- IAM Credentials Reports
  - Account-level report
  - All users in an account and their credentials
- IAM Access Advisor
  - User-level report
  - All service permissions and when they are used
  - Use this tool to review user privileges (least privilege principle)
