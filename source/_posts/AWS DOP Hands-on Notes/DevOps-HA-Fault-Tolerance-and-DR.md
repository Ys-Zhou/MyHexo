---
title: 'DevOps: HA, Fault Tolerance, and DR'
date: 2022-03-23 23:15:30
tags: AWS - DevOps Engineer
---

# ASG

## Create ASG from Launch Configuration
- Old way
- Create Launch Configuration
  - Choose AMI, instance type, and other EC2 instance configure details
  - Add Storage (EBS)
  - Configure SGs
- Create ASG
  - Configure group size, VPC, subnets
  - Configure scaling policies
  - Configure notifications
  - Configure tags (optional to apply to instances)

## Create ASG from Launch Template
- New, recommended way: allow to create a combination of instance types and purchase options
- Create Launch Template
  - Choose to create a template or a template version
  - Able to create from a source template
  - Configure template contents
    - AMI
    - Instance types: allow multiple values or not specify
    - Key pair name: allow not to specify
    - SGs
  - Other configurations
    - ENI, EBS, tags, and etc.
- Create ASG
  - You can create a EC2 instance or Spot Fleet from Launch Template as well
  - Choose template version
  - Able to create an ASG that combine purchase options and instances
  - The rests are same as Launch Configuration

## Scheduled Action
- Configure min, max and desired capacity for an ASG at a schedule (such as corn)

## Scaling policies
- Cooldown: seconds to next scaling action
- If you want to use SQS message depth as a metric, you have to push the metric as a custom metric to CloudWatch Metrics

### Target Tracking Scaling
- Metric types
  - ALB request count per target
  - Avg. CPU utilization
  - Avg. network in
  - Avg. network out
- Warm up: seconds to count instance metric after scaled in
- Able to disable scale-in

### Simple Scaling
- Set a CloudWatch Alarm
- Configure an action when the metric is in alarm
  - Add n instances
  - Remove n instances
  - Set to n instances

### Scaling with Steps
- Configure actions when the metric is in different condition

## ELB Integration
- Configure health check & target group in ELB
- Set the target group to ASG
- Configure health check of ASG from EC2 to ELB

### Slow start duration of target group
- Seconds to receive connection in full capacity

## Suspended Processes
- Suspend some ASG actions for some purpose like troubleshooting
  - Launch, Terminate, HealthCheck, ReplaceUnhealthy, AZRebalance, AlarmNotification, ScheduledActions, AddToLoadBalancer
- If you remove actions from Suspend Processes, the actions will not be redone automatically

## Instance Actions

### Detach Instances
- Instances will be removed from ASG as well as ELBs
- New instances will be launched into the ASG and associated to the ELBs

### Set Instances to Standby
- Instances will be removed from associated ELBs only
- You can set the instances back to InService

### Scale In Protection
- The instances will never be terminated
- You can dynamically call the API from instances to protect
instances to be terminated when they are in processing

## Lifecycle Hooks
- Hooks
  - Pending:Wait -> Pending:Proceed
  - Terminating:Wait -> Terminating:Proceed
- Actions
  - Lambda using CloudWatch Events
  - SNS
  - SQS

## Termination Policies
- Default
  - Balance AZ
  - AllocationStrategy
  - OldestLaunchTemplate / OldestLaunchConfiguration
  - ClosestToNextInstanceHour
  - Random
- OldestInstance
- NewestInstance
- OldestLaunchConfiguration
- OldestLaunchTemplate
- ClosestToNextInstanceHour
- AllocationStrategy

## CloudFormation Integration
- Use CreatePolicy & cfn-signal to make sure that the instances of the ASG is launched
- Update ASG configuration in CloudFormation will not update instances, use UpdatePolicy
  - AutoScalingReplacingUpdate / AutoScalingRollingUpdate
  - AutoScalingScheduledAction (temporarily configuration)

## CodeDeploy Integration
- ASG scale-out action will trigger associated CodeDeploy deployments
- A new ASG will also be created on blue/green deployment as well as a new target group
- When a scale-out action happens during deployment, the new instance will trigger the old deployment
  - It is recommended to suspend actions during CodeDeploy deployment

# DynamoDB
- More than 2 Lambda function read from a DynamoDB stream may cause throttling
  - Use Lambda & SNS to perform fan-out

# EBS
- You can use backup hooked to Terminate Hook to create snapshot into another AZ automatically
- For PIOPS volumns, read the entire volume once (pre-warming blocks) to get max performance after snapshot

# Route 53
- Route 53 health check is integrated with CloudWatch Alarms & Metrics

# Backups
- EFS
  - AWS Backup with EFS
  - Through EC2 (same region)
  - Through S3 CRR & EC2 (cross regions)
- Route 53
  - Use ListResourceRecordSets API for exports
- Elastic Beanstalk
  - Saved configuration
