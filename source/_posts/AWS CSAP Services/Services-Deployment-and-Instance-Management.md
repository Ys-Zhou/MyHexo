---
title: 'Services: Deployment and Instance Management'
date: 2020-08-09 20:32:57
tags: 'AWS - Solutions Architect (Services)'
---

# Elastic Beanstalk

- Platform as a Service
- Components
  - EC2, Auto Scaling Group, ELB, RDS, etc.
  - Users can configure the components
- Beanstalk is free, you just pay for the underlying instances
- Supported platforms
  - Runtime
    - Go
    - Java & Java with Tomcat
    - .NET with IIS
    - Node.js
    - PHP
    - Python
    - Ruby
    - Packer Builder
  - Docker
    - Single container Docker
    - Multi-container Docker
    - Pre-configured Docker
  - Custom platform

## Elastic Beanstalk Architecture Models

- Development model: single instance deployment
- Web application model: ELB+ASG with multi-AZ deployment
- Worker model: SQS+ASG with multi-AZ deployment
  - Can define periodic tasks using *cron.yaml* file

## Route 53 Blue / Green Deployment with Elastic Beanstalk

- Zero downtime and release facility
- Beanstalk is easy to create a new stage and delete the old stage
- Set traffic policies or swap URLs in Route 53

# OpsWorks

- Managed Chef & Puppet
- Work with EC2 and on-premise VM
- OpsWorks is good for Migrating Chef & Puppet from on-premise VM
  - Or SSM is a more cloud-native choice

# CodeDeploy

- Deploy application to 
  - EC2
  - ASG
  - Lambda
  - ECS

## Deploy to EC2

- In-place update EC2 instances (not create new instances)
- Can use hooks to verify after each deployment phase

## Deploy to ASG

- In-place deployment
  - Update existing EC2 instances
  - Newly created EC2 instances will also get automated deployments
- Blue / green deployment
  - Must use ELB
  - Create a new ASG

## Deploy to Lambda

- Use Traffic Shifting feature (like the similar feature in Route 53 and ELB)
- Can use Pre and Post traffic hooks (Lambda functions) to validate deployment
- Can set CloudWatch Alarms to automatically rollback

## Deploy to ECS

- Support Blue / Green deployments for ECS and Fargate
- Setup is in the ECS service definition

# CloudFormation

- Infrastructure as Code in AWS
- CloudFormation is also used by
  - Elastic Beanstalk
  - Service Catalog
  - Serverless Application Model (SAM)

## CloudFormation Policies

- CreationPolicy
  - Define actions to be done after creation (ex. create ASG -> launch EC2 instances)
- UpdatePolicy
  - Define actions to be done after update (ex. update ASG -> update EC2 instances)
- DeletionPolicy
  - DeletionPolicy=Retain
    - Keep resources
  - DeletionPolicy=Snapshot (RDS default)
    - Create a snapshot before deletion
    - Only for services that support creating snapshots
  - DeletionPolicy=Delete (default except RDS)
    - Delete resources
    - S3 buckets will not be deleted if they are not empty

## CloudFormation with IAM

- Deploying CloudFormation stacks need IAM permission
  - Use user IAM principal
  - Assign an IAM role to the stack
- If you create IAM resources, you need explicitly set *CAPABILITY_IAM* or *CAPABILITY_NAMED_IAM* to CloudFormation

## CloudFormation Custom Resources

- Define actions in Lambda functions
- CloudFormation will call the Lambda functions

## CloudFormation Cross and Nested Stacks

- Cross Stacks
  - Use Outputs Export in one stack and use Fn::ImportValue in another stack to pass values
- Nested Stacks
  - Create a stack that includes multiple stacks

## Other Features in CloudFormation

- CloudFormer
  - Create CloudFormation from existing resources
- ChangeSets
  - Preview changes before applying
- StackSets
  - Deploy a CloudFormation stack across multiple accounts and regions
- Stack Policies
  - Prevent accidental updates or deletes to stack resources
