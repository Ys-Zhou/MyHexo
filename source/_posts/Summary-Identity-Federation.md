---
title: 'Summary: Identity & Federation'
date: 2020-05-23 22:43:39
tags: AWS - Solutions Architect
---

# IAM

- Users: long-term credentials
- Groups: group users
- Roles: short-term credentials, uses STS to assume
  - EC2 Instance Roles
  - Service Roles
  - Cross Account Roles
- Policies:
  - AWS Managed
  - Customer Managed
  - Inline Policies

## IAM Policies

- JSON doc
  - Effect
  - Action
  - Resource
  - Condition
  - Policy Variables
- Explicit **Deny** has precedence over **Allow**
  - Use **NotAction** to define "not allow"
- Best practice: use least privilege
  - Access Advisor: see permissions granted and when last accessed
  - Access Analyzer: analyze resources that are shared with external entity
- Policies Conditions
  - "Condition": {"{operator}": {"{key}": "{value}"}}
- Policies Variables and Tags
  - AWS Specific
    - ex. ${aws:username}
  - Service Specific
    - ex. ${s3:prefix}
  - Tag Based
    - ex. ${aws:PrincipalTag/key-name}

## IAM Roles vs. Resource Based Policies

- Both works for cross-accounts accessing
- When you assume a role, you will give up original permissions, and take the permissions assigned to the role
- Resource based policies have no effect on your current permissions
  - S3 buckets
  - SNS topics
  - SQS queues

# STS

## Using STS to Assume a Role

- Define an IAM Role within your account or cross-account
- Define which principals can access the IAM Role
- Use STS to retrieve credentials and impersonate the IAM Role you have access to
- Temporary credentials can be valid between 15 minutes to 1 hour
- Ability to revoke active sessions and credentials for a role

## Benefits

- You must explicitly grant your users permission to assume the role
- Users must actively switch to the role or assume the role
- You can add MFA protection to the role so that only users who sign in with MFA can assume the role

## Assume a Role to 3rd party AWS Accounts

- Use IAM Access Analyzer to find out which resources are exposed
- For granting access to a 3rd party account
  - The 3rd party AWS account ID
  - An External ID
    - To uniquely associate with the role between your account and 3rd party account
    - Must be provided when defining the trust and when assuming the role
    - Must be chosen by the 3rd party
    - In order to prevent the *confused deputy*
  - Define permissions in the IAM policy

## Important APIs

- **AssumeRole**: access a role within your account or cross-account
- **AssumeRoleWithSAML**: return credentials for users logged with SAML
- **AssumeRoleWithWebIdentity**: return credentials for users logged with an Idp
  - AWS recommends using Cognito instead
- **GetSessionToken**: for MFA, obtain user credentials
- **GetFederationToken**: obtain credentials for a federated user