---
title: 'DevOps: Policies and Standards Automation'
date: 2022-03-21 12:08:08
tags: AWS - DevOps Engineer
---

# SSM

## Setup SSM on Instances
- EC2
  - Attach a role with necessary permissions to communicate with SSM
  - Install SSM agent if it was not installed by default
- On-Premise (Hybrid Activations)
  - Install SSM agent
  - Create an activation
    - Create a role used by on-premise instances
    - Get the `Activation Code` and `Activation ID`
  - Register the activation with the SSM agent using code and ID
  - The instance will be registered with an instance ID with the prefix "mi-"
  - Start SSM agent

## Resource Groups
- Group Type
  - Tag based
  - CloudFormation stack based

## Run Command
- Run Command Documents
  - Use the documents in type of `Command`
  - Ex. AWS-UpdateSMMAgent, AWS-RunShellScript
- Targets
  - Specify instance tags
  - Choose instances manually
  - Choose a resource group
- Rate Control
  - Concurrency
  - Error threshold
- Output
  - Write commend output to a S3 bucket

## Parameter Store
- Nothing new

## Patch Manager
- Patch baseline
  - Approval rules
  - Patch exceptions
    - Packages, CVEs, etc.
  - Patch sources
- Maintenance windows
  - Create a maintenance window
  - Register target
  - Register Run Command task (AWS-RunPatchBaseline document)

## Inventory
- Get information from SSM managed instances

## Automations
- Use cases
  - Perform common IT tasks
  - Safely perform disruptive tasks in bulk
  - Simplify complex tasks
    - Ex. creating a AMI after patching a previous AMI

## Session Manager
- Nothing new

# Config

## Configuration
- Configuration / Compliance timeline
- Configuration Details
- Relationships
- Changes
- CloudTrail Events

## Rules
- Rules
  - AWS managed rules
  - Custom rules: use Lambda functions
- Trigger
  - Configuration changes
  - Periodic
- Remediation action

## Automations
- SNS
- CloudWatch Events
- Remediation actions (using SSM Automations)

## Aggregator
- Multi-account & multi-region
- Create a aggregator in the aggregator account
- Create a authorization in the member account

# Inspector

- Install the Inspector agent on EC2 instances
  - If you install agent from inspector console, it will use SSM Run Command to run `AmazonInspector-ManageAWSAgent` document
- Run assessments for the target
  - Network assessments (Inspector agent is not required)
    - Ports reachability
  - Host assessments
    - CVE
    - CIS benchmarks
    - Security best practices
  - Duration: 1 hour by default
  - Schedule: CloudWatch Events will be used
- Review findings and remediate security issues
  - Send messages to a SNS topic (further invoke Lambda functions to remediate the issues)

# Health
- Global Health
- Personal Health
  - You can use CloudWatch Events to perform automation
  - Use case: AWS_RISK_CREDENTIALS_EXPOSED

# Trusted Advisor
- Weekly Email Notification
  - Billing Contact
  - Operations Contact
  - Security Contact
- Automations
  - CloudWatch Events (Trusted Advisor is a global service, so you have to set region to us-east-1)
  - Trusted Advisor Tools (An application, you can deploy it to a Lambda function)
    - You can use Trusted Advisor to detect and remediate exposed keys as well
  - Trusted Advisor Metrics (only available for business and enterprise support)
- Refresh
  - Limitation: once every 5 minutes
  - APIs
    - refresh-trusted-advisor-checks
    - describe-trusted-advisor-checks-*

# GuardDuty
- Intelligent Threat detection
- Logs used
  - CloudTrail Logs
  - VPC Flow Logs
  - DNS query logs

# Macie
- Sensitive data detection in S3
- Support multi-accounts

# Secrets Manager
- Vs. SSM Parameter Store
  - Rotation feature including automatic rotation
  - Credentials for RDS, Redshift, DocumentDB

# License Manager
- Types
  - vCPU
  - Cores
  - Sockets
  - Instances
- Associate to
  - Resources
  - AMIs

# Cost Allocation Tags
- AWS managed tags
  - You can activate them
  - Not shown in Tag Editor
- Custom tags
  - Select tag keys
