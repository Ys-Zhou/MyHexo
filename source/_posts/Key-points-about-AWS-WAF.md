---
title: Key points about AWS WAF
date: 2019-06-16 11:55:02
tags: SA
categories: AWS
---

# 1. Operational Excellence

## Design Principles
* Perform operations as code
* Document everything
* Push small changes instead of big
* Refine operating procedures often
* Anticipate failure
* Learn from the operational failures

## Areas
* Prepare
* Operate (CloudWatch logs, ES, Health Dashboard)
* Evolve

# 2. Security

## Design Principles
* Have a strong identity foundation
* Enable traceability
* Implement security at all layers
* Secure the data
* Automate for security
* Plan for security events

## Best Practices
* Use IAM
* Use detective controls (Config, Config rule, CloudTrail, CloudWatch, VPC flow logs, Inspector)
* Use infrastructure protection
* Use data protection
    * In transit (HTTPS, SSL/TLS, VPN/IPsec, SSH)
    * At rest (Encryption)
* Use incident response (SQLi, XSS, DDoS)

# 3. Performance Efficiency

## Design Principles
* Go global in a few clicks
* Leverage new technologies such as serverless
* Consume advanced technology
* Leverage multiple technologies

## Step 1: Selection
* Compute (instances, containers, functions)
* Storage
* Network
* Database

## Step 2: Review
* Define your infrastructure as code (CloudFormation)
* Use CI/CD
* Run benchmarks and do performance tests regularly

## Step 3: Monitoring

# 4. Reliability

## Design Principles
* Test recovery procedures
* Automate recovery from failure
* Scale horizontally
* Stop guessing capacity
* Automate changes to the system

## Best Practices
* Lay the foundation (Also planned well in advance and envision future growth and integration)
    * Fault isolation zones
    * Redundant components
    * Leveraging managed services
* Implement change management
    * Blue-green deployments
    * Canary deployment
    * Feature toggle
* Implement failure management

# 5. Cost Optimization

## Design Principles
* Choose the best consumption model
* Use managed services
* measure the overall efficiency
* Analyze the expenditure
* Stop spending on a data center

## Areas
* Finding cost-effective resources (Using Trusted Advisor)
* Matching supply with demand
* Being aware of expenditures
* Optimizing over time