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

# Identity Federation

- Federation lets users outside of AWS to assume temporary role for accessing AWS resources
- Without creating IAM users
- Flavors of Federations
  - SAML 2.0
  - Custom Identity Broker
  - Web Identity Federation without Cognito
  - Web Identity Federation with Cognito
  - SSO
  - Non-SAML with AWS Microsoft AD

## SAML 2.0

- Integrate SAML 2.0 compatible IdP or ADFS
- AWS recommends using Amazon SSO instead
- Setup
  - Setup a trust between AWS IAM and SAML (both ways)
  - SAML 2.0 enables web-based cross domain SSO
  - Uses STS API: AssumeRoleWithSAML

## Custom Identity Broker

- Use only if Idp is not compatible with SAML 2.0
- The identity broker talks with STS and must determine the appropriate IAM policy
- Uses STS API: AssumeRole or GetFederationToken

## Web Identity Federation without Cognito

- Get credentials from Web IdP such as Amazon, Google and Facebook
- AWS recommends using Cognito instead
- Uses STS API: AssumeRoleWithWebIdentity

## Web Identity Federation with Cognito

- Create IAM Roles using Cognito with the least privilege needed
- Build trust between the OIDC IdP and AWS
- Benefits
  - Support for anonymous users
  - Support for MFA
  - Data synchronization
- Cognito as a new service replaces Token Vending Machine (TVM)

## Use Web Identity Federation in IAM Policy

- You can identify the user with an IAM policy variable
- Examples
  - cognito-identity.amazonaws.com:sub
  - www.amazon.com:user_id
  - accounts.google.com:sub

# AWS Directory Services

## Microsoft Active Directory (AD)

- Microsoft Active Directory (AD)
  - Centralized security management, create account, assign permissions
- Active Directory Federation Service (ADFS)
  - Provide SSO across applications using AD

## AWS Directory Services

- AWS Managed Microsoft AD
  - Create your own AD in AWS
  - Users are managed in AWS
  - Supports MFA
  - Establish *trust* connections with your on-premise AD
- AD Connector
  - Directory Gateway (proxy) to redirect to on-premise AD
  - Users are managed on the on-premise AD
- Simple AD
  - AD-compatible managed directory in AWS
  - Cannot be joined with on-premise AD

### AWS Managed Microsoft AD

- A managed AD in your AWS VPC
- Integrations
  - EC2 Windows Instance
  - RDS for SQL Server, AWS Workspaces, Quicksight, ect.
  - AWS SSO to provide access to 3rd party applications
- Can be joined to on-premise AD
  - AD two-way forest trust
- Multi-AZ deployment
  - two AZ by default
  - Can be increased for scaling
- Automated backups

#### Connect to on-premise AD

- Must establish a Direct Connect or VPN connection
- Forest trust
  - One-way trust
    - AWS -> On-premise
    - On-premise -> AWS
  - Two-way trust
    - AWS <-> On-premise
- Directly replication between AWS Managed AD and On-premise AD is not supported
  - Solution
    - Replication your on-premise AD to a EC2 Windows instance managed by yourself
    - Establish trust between the AWS Managed AD and AD in EC2
    - For low latency, you can deploy the EC2 instance and AWS Managed AD in one VPC

### AD Connector

- A gateway to redirect requests to your on-premises AD
- Must establish a Direct Connect or VPN connection
- No caching capability
- Does not integrate with AWS services or joining EC2 instances

### Simple AD

- Lower cost, low scale
- Compatible with Microsoft AD
- Supports joining EC2 instances
- Does not support MFA
- Does not integrate with AWS services
- Cannot setup trust relationship

# AWS Organizations

- One master account and many child accounts
  - Master account can invite exists child accounts or create new child accounts
- Master account can access child accounts
  - Create IAM roles in child accounts
  - Assume the roles using the STS Cross Account capability
- Can automatically create AWS account using API
- Integration with AWS SSO

## Feature Modes

- Consolidated Billing Only
  - Consolidated billing across all accounts
  - Pricing benefits from aggregated usage
- All Features (Default)
  - Includes consolidated billing
  - Able to use Service Control Policy (SCP)
  - You can apply an SCP to prevent member accounts from leaving thr organization
  - Invited accounts must approve enabling all features
  - Cannot switch to Consolidated Billing Only

## Organizational Units (OU)

- An OU is a group of accounts and OUs
- Root OU

## Service Control Policies (SCP)

- Applied at the OU or account level
- SCP does not affect service-linked roles
- SCP must have an explicit Allow (does not allow anything by default)
- Blacklist or Whitelist strategies

## Reserved Instances

- Reserved Instances are shared across all accounts
- The payer account (master account) can turn off RI discount and Savings Plans discount sharing for any accounts in the organization

# AWS Resource Access Manager (RAM)

- Share AWS resources that you own with other AWS accounts
- You can choose to share with any account or within you Organization
- Share VPC Subnets
  - Allow different accounts to have resources launched in the same subnets
  - Accounts must be from the same AWS Organizations
  - Cannot share security groups and default VPC
  - One account cannot view, modify, delete, resources that belong to other accounts
- Can also share
  - Route53 Resolver Rules
  - License Manager Configurations
  - etc.

# AWS Single Sign-On (SSO)

- Centrally manage SSO to access multiple accounts and 3rd party applications
- Supports SAML 2.0
- Integration with AWS Organizations
- Integration with AD including on-premise AD
- Centralized permission management
- Centralized auditing with CloudTrail

## SSO vs. AssumeRoleWithSAML

- AssumeRoleWithSAML
  - Use 3rd party IdP Login Portal
    - Return SAML credential
  - Talk with STS to assume a role
- SSO
  - Talk with SSO only to assume a role
    - Use STS under the hook
