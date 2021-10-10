---
title: 'Services and Strategies for Deployment, Operations and Monitoring'
date: 2021-06-12 10:59:46
tags: 'AWS - Solutions Architect (Whitepapers)'
---

# CloudFormation

## Benefits

- Simplify infrastructure management
- Quickly replicate your infrastructure
- Easily control and track changes to your infrastructure

## Components

- AWSTemplateFormatVersion
- Description
- Metadata
- Parameters: provide key-value pairs in run-time
- Mappings: provide map data structures
- Conditions: use condition intrinsic functions to control resources creation
- Transform
- Resources (required)
- Outputs: output values that can be imported into other stacks

## Policies

- Creation Policies
  - Wait on some conditions
  - Supports: AutoScaling::AutoScalingGroup, EC2::Instance, CloudFormation::WaitCondition
- Deletion policies
  - You can preserve or backup a resource when the stack is deleted
  - Attributes
    - Delete
      - Default attribute except RDS
      - For S3 buckets, the buckets must be empty or the deletion will be failed
    - Retain
    - Snapshot
      - Supports: EBS, Elasticache, RDS (default), Redshift

## Other features

- Stacks can be nested
- Use output section and import function to perform cross-stack reference
- CloudFormation integrates with CloudTrail

## StackSets

- CloudFormation StackSets allows you to create, update, delete stacks across multiple accounts and regions
- StackSets use user's IAM permissions or stack service role

# OpsWorks

## Stacks

- OpsWork stack is basically a container for resources - EC2 instances, RDS, DynamoDB, ELB (CLB only), on-premise instances
- Stacks help to manage these resources as a group
- A stack defines some default configuration, such as instances' operating system, AWS region

## Layers

- A stack has one or more layers
- A Layer represents a set of resources that serve a particular purpose
- Layers give complete control over which packages are installed, how they are configured, how applications are deployed, and more
- Layers depend on Chef recipes to handle tasks such as
  - Installing packages on instances
  - Deploying apps
  - Running scripts
- You package your recipes and related files in one or more cookbooks and store the cookbooks in a repository (S3 or Git)
- After you create a layer, some properties (such as AWS region) are immutable, but you can change most of the layer configuration at any time

## Life Cycle Events

- You can run your recipes manually, you can also automate the process in LifeCycle Events
  - Setup: after instance successfully boots
  - Deploy: when you deploy an app
  - Configure: when one of the instances enters or leaves the online state
  - Undeploy: when you delete an app
  - Shutdown: when you stop an app

## Instances

- OpsWorks Instances represent a single computing resources, such as EC2 instances
- After EC2 instance has finished booting, OpsWorks will install an agent that handles communication and run recipes
- An instance can belong to multiple layers (not recommended)
- Instances types
  - All-time instances
  - Time-based instances
  - Load-based instances
    - Only support Linux-based stacks
- Auto healing
  - If an agent stops communicating, OpsWorks will automatically restart the instance

## Integration with IAM

- Control access
  - Between users and stacks or resources in the stacks
  - Service control between stacks and resources
  - Service control between apps in stacks and other AWS services

## Allocating IPs

- OpsWorks will try to assign an Elastic IP first
- If not, OpsWork will try to assign a public IP
- If not as well, the instance will only has a private IP
  - In this case, you may need an NAT to ensure the instance can communicate with Internat

## Integration with ELB (CLB)

- OpsWorks only support CLB (not ALB)
- Attach a CLB:
  - De-registers any currently registered instances
  - Registers the layer's instances
  - Registers new instances as they come online
  - De-registers instances when they become offline
  - Automatically activates and deactivates the instances' AZ
- Relationship between CLB and OpsWorks Layer is 1:1

## Monitoring and Logging

- CloudWatch
  - Custom CloudWatch metrics for Linux stacks
  - Standard CloudWatch metrics for Windows stacks
- CloudTrail
  - Record API calls made by OpsWorks
- OpsWorks event logs
  - Lists all events in your stack
- Chef logs
  - Lifecycle events on each instance

## UnrecognizedClientException

- Occurs if a resource outside OpsWorks on which the instance depends was edited or deleted
  - The IAM user or role associated with the instance has been deleted
  - Instance volume or storage configurations are edited while the instance is offline
  - Adding an EIP to EC2 instances manually

# Elastic Beanstalk

- Elastic Beanstalk is a PaaS for deploying Web applications
  - Programming languages: Java, .NET, PHP, Node.js, Python, Ruby, Go
  - Servers and containers: Apache, Nginx, Passenger, Puma, Docker, Tomcat, IIS
  - Users can custom their own platforms
- Users upload their code, and Elastic Beanstalk handles the details of capacity provisioning, load balancing, scaling, and application health monitoring
- Elastic Beanstalk has no persistent local storage
  - Use S3, EFS, DynamoDB, RDS
- Elastic Beanstalk will be unusable if you modify the underlying AWS resources
- You can rebuild the environment within 6 weeks after you terminate the environment or the environment became unusable

## Application

- An Elastic Beanstalk application is a logical collection of Elastic Beanstalk components, including environments, versions, and environment configurations
- Deleting an application will delete all components above
- Environments
  - Web server environment
  - Worker environment: pulls tasks from SQS

- For Docker applications
  - Elastic Beanstalk use EC2 instances for single container configuration, ECS for multi-container configuration
  - Elastic Beanstalk has some pre-configured Docker configurations if it matches the software used by your application, but you can customize your own Dockerfile

## Integration

- S3
  - Elastic Beanstalk creates an S3 bucket for each region in which you create environments
  - Application versions, custom platform related data, source bundles are saved in the S3 bucket
  - Elastic Beanstalk can monitor your environment app's logs and publish them to S3
- EFS
  - You can use EFS as local storage
- RDS
  - You can include RDS instances in test or dev Elastic Beanstalk environments, but it's not recommended
  - It is recommended to use RDS instances outside your Elastic Beanstalk environments
- DynamoDB
  - You can use configuration files to create DynamoDB tables outside your Application
- CloudWatch
  - Instance logs (use CloudWatch Agent)
  - If you enable enhanced health, you can stream health information (software, ELB, ASG,...) to CloudWatch Logs (use health agent preinstalled in AMI for each platform configuration that supports enhanced health)

## Patterns and Anti-patterns

- Patterns
  - Quickly deploy an application
  - Minimal knowledge about AWS infrastructure
  - Migrating Web application from on-premise servers to AWS
  - Docker application
- Anti-patterns
  - If users want to control the AWS resources

# CloudWatch

## Namespaces

- Namespaces are containers for CloudWatch metrics
- Metrics in different namespaces are isolated form each other
- AWS namespaces are AWS/service
- Create a custom metrics need to specify a namespace for each data point

## Metrics

- AWS services send metrics to CloudWatch, and users can create custom metrics
- Metrics is not cross-region
  - You can create CloudWatch Dashboard from metrics in multiple region
- Metrics are uniquely defined by name, namespace, and optional dimensions
- The timestamps of metrics can be up to 2 weeks in the past or 2 hours into the future

### Metrics Retention

- Metrics cannot be deleted
- CloudWatch retains metric as follows:
  - Data points with a period of less than 60 seconds (called high-resolution metrics) are available for 3 hours
  - 60 seconds (1 minute) are available for 15 days
  - 300 seconds (5 minutes) are available for 62 days
  - 3600 seconds (1 hour) are available for 455 days (15 months)
- Shorter period are aggregated to longer period
  - 60 -> 300 -> 3600
- After 15 months, metrics expire on a rolling basis
- Metrics expire after 15 months if no new data is published to them

## CloudWatch Alarms

- An alarm can be created based on:
  - one metric
  - the result of a math expression based on metrics
  - CloudWatch Logs metric filters
- The alarm can perform one or more actions based on the threshold
- The possible alarm states:
  - OK
  - ALARM (Alarm is triggered)
  - INSUFFICIENT_DATA (The alarm has just started or the metrics are not available)
- ALarms can be added to CloudWatch dashboards
- Alarm settings:
  - Period in seconds
  - The number of period to evaluate
  - The number of period to alarm

### Alarm Actions

- EC2 actions
- Auto Scaling actions
- Sending notifications to a SNS topic 
- CloudWatch Alarms can only perform 3 types of action above, other actions can be performed by CloudWatch Events

## CloudWatch Logs

- CloudWatch Logs is a service can be used to store and monitor logs from many AWS and Non-AWS sources
- You can:
  - View the logs as time series
  - Query the logs
  - Filter the logs based on specific fields
  - Archive the logs securely for future analysis
- You can also:
  - Create powerful queries using CloudWatch Insights
  - Visualize the log data in CloudWatch Dashboards

### Log data sources
- EC2 instances (Requires CloudWatch Agents)
  - Or any services that use EC2 instances
- CloudTrail
- Route 53 DNS query logs
- RDS (Aurora, MySQL, MariaDB)
- Amazon Neptune
- VPC Flow Logs
- API Gateway Execution logging
- Lambda function logs
- On-premise instances (Requires CloudWatch Agents)

### Logs

- Log Events
  - Event timestamp
  - Event message
- Log Streams
  - A sequence of log events from the same source
  - Each log stream has to belong to a log group
- Log Group
  - Groups of log streams that share the same settings
  - There is no limit on the number of log streams that can belong to a log group
- Log Retention
  - By default, logs never expire (Log data storage is charged)
  - Users can set the retention policy for each Log Group
- Metric Filters
  - You can use filters to configure custom CloudWatch metrics form CloudWatch logs
  - Metric filters are assigned to log groups
  - CloudWatch Logs sends metrics to CloudWatch every minute

### Encryption

- Encryption in Transit
  - CloudWatch Logs manages the encryption keys
- Encryption at Rest
  - CloudWatch Logs manages the server-side encryption keys
  - You can also manage the keys using AWS KMS CMK
  - Encryption is enabled at the log group level
  - You can also encrypt logs when you create the log group or after it exists
