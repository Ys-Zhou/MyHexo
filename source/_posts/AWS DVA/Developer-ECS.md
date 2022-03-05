---
title: 'Developer: ECS'
date: 2022-02-09 16:28:43
tags: AWS - Developer
---

# ECS

## Roles

- ecsInstanceRole
  - Assigned to EC2 instances running ECS services
  - Included actions:
    - Make API calls to ECS service
    - Send logs to CloudWatch Logs
    - Pull Docker image from ECR
- ecsTaskExecutionRole
  - Assigned to ECS tasks
  - Define permissions the tasks need
  - You have to enable `ECS_ENABLE_TASK_IAM_ROLE` option in `ecs.config` file
- Other roles: ecsServiceRole, AWSServiceRoleForECS

## Task Placement

- Task Placement will be evaluate when you or ASG add or delete a task

### Task Placement Process

1. Satisfy the CPU, memory and port requirements in the task definition
2. Satisfy the task placement constraints
3. Satisfy the task placement strategies (best effort)

### Task placement constraints

- distinctInstance
  - Place tasks on different container instances
- memberOf
  - Define custom constraints use Cluster Query Language

### Task Placement Strategies

- Binpack
  - Place tasks based on the least available amount of *CPU* or *memory* to minimize the number of instances
  - *field* can be:
    - *CPU*
    - *memory*
- Random
- Spread
  - Place the tasks evenly based on the *field* you specify
  - *field* can be:
    - *instanceId*
    - *attribute:esc.availability-zone*
- You can mix three strategies

## Capacity Provider

- ECS service auto scaling can only scale at task level not instance level
- Service auto scaling action will be failed if EC2 instances are insufficient
- You can use Fargate or Capacity Provider
- Use Capacity Provider
  - Create a ECS capacity provider associated to an ASG
  - Create a service to use the provider

## Volumes

- Bind Mount
  - Mount the instance storage (EC2 mode) or 4 GB storage (Fargate) to the task
  - Useful for sidecar pattern
- Docker
  - Mount the EBS volume (mounted to the EC2 instance) to the task 
  - Scope can be task (destroyed after stop) or shared (retained after stop)
- EFS

# ECR

- Push a Docker image to ECR
  - docker login
    - CLI v1: Run the output of `aws ecr get-login --no-include-email --region your-region`
    - CLI v2: Run `aws ecr get-login-password --region your-region | docker login --username AWS --password-stdin your-ecr-resource-url`
  - `docker build -t your-image .`
  - `docker tag your-image:latest your-ecr-resource-url/your-repo:latest`
  - `docker push your-ecr-resource-url/your-repo:latest`
