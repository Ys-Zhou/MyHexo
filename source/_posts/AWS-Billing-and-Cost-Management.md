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
- Policies
  - Policies can be applied to Root, Organizational Units, or Accounts
  - Parent policies will flow to children
  - To grant the permissions allowed by organizational policies, you still need use IAM

## Features

- Centralized management of all the accounts in the organization
- Consolidated Billing for accounts in the organization
- Hierarchical grouping of accounts to meet budgetary, security, or compliance
  - You can nest Organizational Units within other Organizational Units at a depth of 5 levels
- Enables access control to AWS services and APIs
  - Master Account has authority over the administrators of the Member Accounts in the organization
- AWS Organizations configurations are eventually consistent
  - The data will be stored immediately, but it takes time to replicate

# AWS Billing Management

## AWS Cost Explorer

## AWS Budgets
