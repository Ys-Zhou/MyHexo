---
title: 'DevOps: SDLC Automation'
date: 2022-02-23 12:12:23
tags: AWS - DevOps Engineer
---

# CodeCommit

## Connect to repo
- SHH connections for a root account cannot be configured
- HTTPS connections for a root account are not recommended
- SHH keys and HTTPS Git credentials for CodeCommit can be set for a IAM user

## Deny push to master
- Use IAM policy with "Deny" effect
  - "Condition" section:

```json
"Condition": {
    "StringEqualsIfExists": {
        "codecommit:References": [
            "refs/heads/master"
        ]
    },
    "Null": {
        "codecommit:References": false
    }
}
```

## Set notification rules
- Trigger events include
  - Comments
  - Pull request
  - Branches and tags
- Targets
  - SNS topic only

## Set triggers
- Trigger events include
  - All repository events
  - Push to existing brach
  - Create branch or tag
  - Delete branch or tag
- Targets:
  - SNS topic
  - Lambda function

## Use EventBridge (CloudWatch Events) rules
- Notifications and triggers created in CodeCommit actually use EventBridge rules
- You can use EventBridge directly to create more powerful rules integrated with more services

# CodeBuild

## Create build project
- Sources
  - S3
  - CodeCommit or other git repository services
- Reference types
  - Branch
  - Git tag
  - Commit ID
- Environment image
  - Managed image
  - Custom image
- Service role
- Additional configurations
  - Timeout: 5 min ~ 8 h
  - VPC
  - Compute: memory & vCPU
- Buildspec
  - It is recommended to use buildspec.yaml file
- Artifacts
- Logs
  - Optional to CloudWatch Logs or/and S3

## buildspec.yaml template

```yaml
version: 0.2
run-as: "user-name"
env:
  variables:
    "key": "value"
  parameter-store:
    "key": "path"
  git-credential-helper: "yes/no"
phases:
  install:
    run-as: "user-name"
    runtime-versions:
      "runtime": "version"
    commands:
      - "command"
    finally:
      - "command"
  pre_build/build/post_build:
    run-as: "user-name"
    commands:
      - "command"
    finally:
      - "command"
artifacts:
  files:
    - "location"
  name: "artifact-name"
  discard-paths: "yes/no"
  base-directory: "location"
  secondary-artifacts:
    "artifactId":
      files:
        - "location"
      name: "secondary-artifact-name"
      discard-paths: "yes/no"
      base-directory: "location"
cache:
  paths:
    - "path"
```

## buildspec.yaml docker sample

```yaml
version: 0.2
phases:
  install:
    runtime-version:
      docker: 18
  pre_build:
    commands:
      # Need a service role with ECR permissions
      - $(aws ecr get-login --no-include-email -- region $AWS_REGION) # CLI v1
  build:
    commands:
      - docker build ...
      - docker tag ...
  post_build:
    commands:
      - docker push ...
```

## Environment variables
- Pre-set variables
- Configure in buildspec.yaml
- Use Environment Variables Override when start build
  - Can reference SSM Parameter Store parameters (need necessary SSM permissions)

## Artifacts configuration
- Packaging: None / Zip
- Artifacts is default encrypted by CodeBuild (use KMS). You can disable artifact encryption and enable S3 default encryption
- The service role need necessary S3 permissions

## CloudWatch Metrics
- Succeeded Builds Sum
- Failed Builds Sum
- Builds Sum
- Duration Average

## EventBridge (CloudWatch Events)
- CloudBuild project can be a target

# CodeDeploy

## Deploy to instances (not ASG)

### Setup instances

#### Setup EC2 instances
- CodeDeploy must be installed
- Need permissions to fetch artifacts from S3
- Tag the instances for creating deployment groups

#### Setup on-premise instances
- Create an IAM user per instance
  - Grant necessary S3 read permissions
  - Add access_key_id, secret_access_key and user_arn to CodeDeploy agent configuration file
  - Configure AWS CLI using the same access_key_id and secret_access_key
  - Install CloudDeploy agent
  - Register the on-premises instance with CodeDeploy
  - Tag the on-premises instance
- Create an IAM role (recommended)
  - Instead of using IAM users, use STS to generate temporary credentials

### Create deployment groups

#### Deployment configuration for in-place deployment
- Default: OneAtATime, HalfAtATime, AllAtOnce
- Customize

#### Load balancing
- Use load balancer to manage incoming traffic during the deployment process
  - ALB
  - CLB
  - Disable

#### Rollbacks
- Roll back when a deployment fails
- Roll back when alarm thresholds are met
  - Integrated with CloudWatch Alarms

### appspec.yaml template

```yaml
version: 0.0
os: linux
files:
  - source: "source path"
    destination: "destination path"
hooks:
  "LifecycleEventName":
    - location: "script file path"
      timeout: "timeout seconds"
      runas: "user name (ex. root)"
```

### Lifecycle event hooks
- Common
  - ApplicationStop -> DownloadBundle* -> BeforeInstall -> Install* -> AfterInstall -> ApplicationStart -> ValidateService
- Blue/green
  - BeforeBlockTraffic -> BlockTraffic* -> AfterBlockTraffic
  - BeforeAllowTraffic -> AllowTraffic* -> AfterAllowTraffic

### Environment variables for hooks
- APPLICATION_NAME
- DEPLOYMENT_ID, DEPLOYMENT_GROUP_NAME, DEPLOYMENT_GROUP_ID
- LIFECYCLE_EVENT

### EventBridge (CloudWatch Events)
- CloudDeploy state change can set as trigger
- Use cases:
  - Trigger Lambda functions
  - Sent messages to Kinesis Data Streams
  - Trigger CloudWatch Alarm

### CloudWatch Logs
- Must install CloudWatch Logs agent just like CodeDeploy agent

## Deploy to Lambda

### Deployment configuration
- Canary
- Linear
- All-at-once

### Lifecycle event hooks
- BeforeAllowTraffic -> AllowTraffic* -> AfterAllowTraffic

# CodePipeline

## Settings
- Service role
- Artifact store: Default / custom location (S3 bucket)
- Encryption key: Default AWS / Customer managed key (in KMS)

## Stages

### Source
- CodeCommit, ECR, S3, Github
- Detection options
  - CloudWatch Events 
  - CodePipeline (check periodically for changes)

### Build
- CodeBuild, Jenkins
- For CodeBuild, artifacts will be upload to S3 buckets defined in buildspec.yaml (CodeBuild) as well as configured in CodePipeline

### Deploy
- CodeDeploy
- S3
  - Upload artifacts to another S3 bucket
- CloudFormation
  - Create or update a stack
  - Delete a stack
  - Replace a failed stack
  - Create or replace a change set
  - Execute a change set
- Elastic Beanstalk, Service Catalog, ECS, etc.

### Test
- CodeBuild, Jenkins, etc.

### Approval
- Manual approval
  - Option to set a SNS topic and URL for review

### Invoke
- Lambda
  - APIs & Permissions
    - codepipeline:PutJobSuccessResult
    - codepipeline:PutJobFailureResult
  - continuationToken parameter in the APIs identify the pipeline job

## EventBridge (CloudWatch Events)
- You can set other rules to trigger CodePipeline besides detection option
- CodePipeline can also be a trigger (ex. state change)

## Run orders
- If you define your CodePipeline pipeline in code, you can use run orders to define action groups and actions
- Different action groups have different run order, will be executed from 1, 2, 3, ...
- Actions in the same action group have the same run order

# CodeStar

- Quickly start a project with CI/CD
  - Application category
  - Programming languages
  - AWS services: Elastic Beanstalk, EC2, Lambda
  - Repository: CodeCommit, GitHub
  - IDE
- A central view of the project
  - Dashboard
  - CodeCommit, CodeBuild, CodePipeline

## template.yaml
- CodeStar configuration file
- Start with
```yaml
Transform:
  - AWS::CodeStar
```

# Jenkins on AWS

- Can replace CodeBuild, CodePipeline, CodeDeploy
- Must be deployed in a Master / Workers configuration on EC2 instances
  - You can deploy your master and worker on the same instance or in different instances (recommended)
  - You can deploy Jenkins servers in different AZ for High availability (multi-master deployment)
- Use a configuration file called Jenkinsfile (similar to buildsped.yml)

## Jenkins integrations
- CodePipeline
- ECR & ECS
- Device Farm
- Lambda
- CloudFormation

## Jenkins plugins
- AWS EC2: Start instances and automatically connect them as Jenkins agents. Terminated the instances when the load goes down.
- AWS CodeBuild: Let CodeBuild manages the EC2 instances
- Amazon Elastic Container Service & Amazon EC2 Container Service plugin with autoscaling capabilities: Launch Jenkins master / workers in ECS
- AWS CodePipeline: Allow CodePipeline to run jobs on Jenkins
- Artifact Manager on S3: Keep artifacts and pipeline stashes in S3

# Blue/Green Deployments

- Update DNS routing with Route 53
- Swap the ASG behind the ELB
- Update ASG launch configurations and scale the ASG to twice size
- Swap the environment of an Elastic Beanstalk application
- Clone a stack in OpsWorks and update DNS

# Whitepapers

- Implementing GitFlow on AWS services
- Practicing Continuous Integration Continuous Delivery on AWS
- Jenkins on AWS
