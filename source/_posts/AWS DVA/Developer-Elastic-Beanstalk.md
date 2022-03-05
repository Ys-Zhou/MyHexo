---
title: 'Developer: Elastic Beanstalk'
date: 2022-02-09 16:26:50
tags: AWS - Developer
---

# Deployment Options
  
- Deployment policy
  - All at once
  - Rolling
  - Rolling with additional batch
  - Immutable
  - Traffic splitting
- Blue/Green deployment
  - Swap environment URLs after Immutable deployment

# Lifecycle Policy

- Policies
  - Based on time (remove old versions)
  - Based on space
- Current version won't be deleted
- Option not to delete the source bundle in S3

# Extensions

- All the parameters set in the UI can be configured using these files
- Setting
  - Files are in `root/.ebextensions/` with extensions of `.config`
  - Format can be YAML or JSON
  - Modify some default settings using `option_settings` property
  - Able to add resources such as RDS, ElastiCache, DynamoDB, ...
- Resources managed by extensions will be deleted if the environment is deleted

# Cloning

- Create an environment with the exact same configuration

# Migrations

## Change the ELB Type in an environment

1. Create a new environment with the same configuration except ELB (do not clone)
2. Deploy your application onto the new environment
3. Swap CNAMEs or update Route 53 records

## Decouple RDS from an environment

1. For save, create a snapshot of the RDS instance
2. Protect the RDS instance from deletion from RDS console
3. Create a new environment without RDS and point to the existing RDS instance
4. Swap CNAMEs or update Route 53 records
5. Terminate the old environment
6. Delete CloudFormation stack (will be failed but it's OK)

# Use Docker

- Single-container Docker
  - Provide a Dockerfile or `Dockerrun.aws.json` (v1) file at your code root
  - Use EC2 with Docker, not ECS
- Multi-container Docker
  - Provide a `Dockerrun.aws.json` (v2) file at your code root
  - Use ECS (task definition is in `Dockerrun.aws.json` file)

# HTTPS

- Load the SSL certificate onto the ELB
  - Execute form the Console
  - Define in .ebextensions/securelistener-alb.config
- Redirect HTTP to HTTPS
  - Configure your instances to redirect HTTP to HTTPS
  - Configure the ALB rule (only for ALB)
  - Make sure health checks are not redirected

# Custom Platform

- Use custom platform only if your applications are incompatible with provided platforms and Docker
  - You can create a custom AMI to tweak the current platform rather than create a entire custom platform
- Build custom platform using Packer software (defined in `Platform.yaml` file)
