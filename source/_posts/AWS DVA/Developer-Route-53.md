---
title: 'Developer: Route 53'
date: 2022-02-19 19:57:47
tags: AWS - Developer
---

# Route 53

- Authoritative DNS Service & Domain Registrar
  - Authoritative: customers can update the DNS records

# CNAME & Alias

- CNAME
  - hostname -> hostname
  - Can not be used for root domain (Zone Apex)
- Alias
  - hostname -> AWS resource hostname (amazonaws.com)
  - free
  - Native health check
  - Records Targets: ELB, CloudFront Distributions, API Gateway, Elastic Beanstalk environments, S3 Websites, VPC Interface Endpoints, Global Accelerator accelerators, Route 53 records in the same hosted zone
    - **Not include EC2 DNS name**

# Health Checks

- Health Checks are only for public resources
  - Public endpoint
  - Other health checks (calculated health checks)
  - CloudWatch Alarms

# Traffic Flow

- A visual tool to create complex route policies
- Use this tool to create Geoproximity Policy
  - Set bias
