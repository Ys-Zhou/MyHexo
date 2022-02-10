---
title: DVA - Code
date: 2022-02-09 16:24:46
tags: AWS - Developer
---

# CodePipeline

- Stages
  - Source (Git repo, ECR, S3)
  - Build
  - Deploy
- Multiple action groups in a stage (action groups serial)
- Multiple actions in a action group (actions are parallel)

# CodeBuild

- Source: Git repo, S3
- Build instructions: `buildspec.yml` file at the root
- Logs can output to S3 and CloudWatch Logs
- Integration with CloudWatch Metrics, Events, Alarms
- CodeBuild projects can be defined within CodePipeline
- Use S3 as cache and artifactory
- You can optionally run CodeBuild within your VPC
- You can leverage the CodeBuild Agent to run CodeBuild locally

## buildspec.yml
  - env
    - variables
    - parameter-store
    - secrets-manager
  - phases
    - install
    - pre_build
    - build
    - post_build
  - artifacts
  - cache

# CodeDeploy

- IAM roles
  - ColdDeploy role
  - EC2 role (permissions to pull artifacts from S3 or Github)
- Targets
  - EC2/On-premises (Must running CodeDeploy Agent)
  - ECS
  - Lambda
- Deployment Group
  - Tagged EC2/on-premise instances
  - ASG
  - Mix of tagged instances and ASG
  - Customization in scripts with `DEPLOYMENT_GROUP_NAME` environment variables
- Deploy type
  - In-place
    - One At A Time
    - Half At A Time
    - All At Once
    - Custom
  - Blue/green
- Failure Handling
  - Redeploy old deployment or enable automated rollback
    - CodeDeploy will deploy the last known succeeded revision as a new deployment
  - New deployments will first be deployed to failed instances
- Deploy instructions: `appspec.yml` file at the root

## appspec.yml
  - files
    - source
    - destination
  - hooks
    - ApplicationStop
    - DownloadBundle
    - BeforeInstall
    - Install
    - AfterInstall
    - ApplicationStart
    - ValidateService

# CodeStar

- A unified view of projects & quickly create CICD-ready projects
- Automatically create underline resources
- Issue tracking integration with JIRA / Github
- Ability to integrate with IDEs
- Service itself is free

# CodeArtifact

- AWS managed artifactory

# CodeGuru

- ML-powered service for
  - automated code reviews (CodeGuru Reviewer)
    - critical issues
    - vulnerabilities
    - hard-to-find bugs
  - application performance recommendations (CodeGuru Profiler)
