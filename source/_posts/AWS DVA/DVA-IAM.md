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

# Dynamic Policies

- Use policy variables in policies
  - Ex. `${aws:username}`

# Pass Roles

- For the roles to be passed
  - Must trust the targets (using trust policies defining targets in the Principal section)
- For users who take the action
  - Require `iam:PassRole` permission
  - Need `iam:GetRole` if you want to view the role

# Security Tools

- IAM Credentials Reports
  - Account-level report
  - All users in an account and their credentials
- IAM Access Advisor
  - User-level report
  - All service permissions and when they are used
  - Use this tool to review user privileges (least privilege principle)
- IAM Access Analyzer
  - Identify the resources in your organization and accounts that are shared with an external entity
