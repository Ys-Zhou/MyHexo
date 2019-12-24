---
title: AWS Billing and Cost Management
date: 2019-12-17 09:12:00
tags: AWS - Solutions Architect
---

# AWS Organizations

- Consolidate multiple AWS accounts into a single organization
- Free of charge
- Global Service (like IAM)
- Can only be reached through an endpoint in *us-east-1* region

## Components

- Root
  - Root is created automatically when you created the organization
  - One Root in an organization
  - Root has Organizational Units and Accounts
- Organizational Units
  - An Organizational Unit has Organizational Units and Accounts
- Accounts
  - An organization has a Master Account
    - The account created the organization
    - Is also Payer Account
    - Manage the organization
    - Apply organizational policies
  - The other accounts are called Member Accounts
  - An account can be a member of only one AWS organization at a time
- Service Control Policies
  - SCPs can be applied to Root, Organizational Units, or Accounts
  - Parent policies will flow to children
    - The root has *FullAWSAccess* policy by default
    - Children SCPs can not override parent SCPs
  - SCPs do not grant permissions
    - To grant the permissions allowed by SCPs, you still need use IAM
  - SCPs have no effect on the master account

## Features

- Centralized management of all the accounts in the organization
- Consolidated Billing for accounts in the organization
- Hierarchical grouping of accounts to meet budgetary, security, or compliance
  - You can nest Organizational Units within other Organizational Units at a depth of 5 levels
- Enables access control to AWS services and APIs
  - Master Account has authority over the administrators of the Member Accounts in the organization
- AWS Organizations configurations are eventually consistent
  - The data will be stored immediately, but it takes time to replicate

## Feature Sets

- AWS Organizations has two modes
  - Consolidated billing
    - Default mode
    - Only consolidated billing is enabled
  - All features
    - Includes all features of AWS Organizations
      - Full control over member accounts
      - Apply Service Control Policies
      - Prevent member accounts from leaving the organization
    - Can be set for a newly created organization or moved from consolidated billing mode
      - In case of moving from consolidated billing, member accounts must approve the invitation first

## Integration

- CloudTrail
  - Central logging of all accounts logs in the organization
- CloudWatch
  - Can enable sharing of CloudWatch events across all accounts in the organization
- AWS Config
  - Obtain an organization wide view of your configurations of resources in all accounts
- Artifact
  - Allows to download compliance reports such as PCI and ISO for the organization
- AWS Firewall Manager
  - Centrally manage WAF configurations across all accounts in the organization
- AWS Directory Service
  - Seamless directory service sharing across multiple accounts and any VPC in a region
- AWS License Manager
  - Can ensure license compliance of all the resources in the organization
- AWS Resource Access Manager (RAM)
  - Allows for sharing resources across accounts in the organization
- AWS Service Catalog
  - Enables the sharing and management of products across accounts in the organization from catalogs of IT services approved for use on AWS
- AWS Single Sign-On (SSO)
  - Allows users to use SSO to access their assigned accounts (master or member)

## Best Practices

- Use AWS CloudTrail to monitor and log activities in the master account
- Do not add resources in the master account
- Use the least privilege principle for SCPs
- Assign ACPs at Organizational Units instead of accounts for easy scaling
- Avoid assigning SCPs at the root level, unless absolutely necessary
- Test your SCPs first on an account before rolling out
- Use either Whitelisting or Blacklisting, not both
- Establish a clear strategy as to when to create a new account

# AWS Billing Management

## AWS Cost Explorer

- View cost data as a graph
  - Can filter graphs by different values
  - Can filter by member account when using consolidated billing
- Get forecast of future costs based on the historical cost data

## AWS Budgets

- Plan service usage, costs, and instance reservations
- Budgets provide:
  - The resources usage to date, including Reserved Instances
  - The set budgets usage
  - The predicted usage will incur in charges bu the end of the month
- Can send notifications using SNS
  - When the usage goes over the budgeted amounts
  - When the estimated costs exceed the configured budgets
- Budgets information is updated up to three times a day
- Budget types
  - Cost budgets
  - Usage budgets
  - Reserved Instances utilization budgets
    - Alert when the usage of Reserved Instances falls below the threshold
  - Reserved Instances coverage budgets
    - Alert when the instance hours covered by Reserved Instances fall below the threshold
- AWS Budgets uses AWS Cost Explorer to visualize costs

25? 27-31
