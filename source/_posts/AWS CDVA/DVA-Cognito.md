---
title: DVA - Cognito
date: 2022-02-19 19:56:40
tags: AWS - Developer
---

# Cognito User Pools

- Return JSON Web Token (JWT) credentials
- Features
  - Simple login: username + password stored in the database
  - Password reset
  - Email & phone number verification
  - MFA
  - Federated identities
  - Block users if their credentials are compromised
- Integration
  - API Gateway
  - ALB
- Lambda Triggers
  - Invoke a Lambda function on many triggers
- Hosted Authentication UI

# Cognito Identity Pools

- Get temporary AWS credentials
- Providers
  - Federated providers
  - Cognito User Pools including guest access
  - OpenID & SAML
  - Custom providers
- IAM Roles
  - Default roles for guest users
  - Role rules based on user id
  - Roles must have a trust policy of Cognito Identity Pools