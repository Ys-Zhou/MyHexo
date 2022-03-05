---
title: 'Developer: X-Ray'
date: 2022-02-11 21:26:01
tags: AWS - Developer
---

# Compatibility

- Lambda
- Elastic Beanstalk
- ECS
- ELB
- API Gateway
- EC2 or on-premise instances

# Usage

- Use X-Ray SDK in your application code
- Install the X-Ray daemon or enable X-Ray integration
  - Need `AWSX-RayWriteOnlyAccess` policy for the service role

## Enable X-Ray in Elastic Beanstalk

- Enable X-Ray in .ebextensions
- This method is not work for Multi-container

```yaml
# .ebextensions/xray-daemon.config
option_setting:
  aws:elasticbeanstalk:xray:
    XRayEnabled: true
```

## Use X-Ray in ECS

- Run a X-Ray daemon container in each EC2 instance (only for EC2 mode)
  - One EC2 instance, one X-Ray daemon container, many app containers
- Use X-Ray daemon container as a sidecar (for both EC2 & Fargate mode)
  - One app container, one X-Ray daemon container

# Concepts

- Segments: X-Ray information field for each application and service
- Subsegments: subfields of a segment
- Trace: segments collected from requests end-to-end
- Sampling: sample the requests sent to X-Ray to reduce cost
- Annotations: Key-value pairs used to index traces, can be used in searching
- Metadata: Key-value pairs without being indexed, cen not be used in searching

# Sampling Rules

- Reservoir - n : n requests will be sent per second
  - Default: n=1
- Rate - m : m of the other requests will be sent
  - Default: m=0.05

# APIs

## Write APIs

- PutTraceSegments
- PutTelemetryRecords: For daemon to send telemetry records to X-Ray service
- GetSamplingRules

## Read APIs

- GetServiceGraph
- BatchGetTraces: Get traces by specified IDs
- GetTraceSummaries: Get IDs and annotations of traces for a specified time
- GetTraceGraph
