---
title: 'Developer: API Gateway'
date: 2022-02-17 17:18:04
tags: AWS - Developer
---

# Endpoint Types

- Edge-Optimized
- Regional
- Private (through an VPC endpoint)

# Integration Types

- MOCK: no backend
- AWS_PROXY / HTTP_PROXY: requests are passed to backends
- HTTP / AWS: generate requests to HTTP / AWS backends based on the requests to API Gateway
  - Use Mapping Templates to modify requests
  - Use cases:
    - Transform JSON requests to XML requests
    - Put query parameters of URL into a Lambda event

# Swagger / Open API 3 Integration

- Import REST API definition from
- Export REST API definition to

# Usage Plans & API Keys

- You can create usage plans to APIs and associate then with API keys
- Usage Plan
  - Throttling
  - Quota
- An Api key must be provided in `X-API-KEY` header

# CloudWatch Metric

- CacheHitCount & CacheMissCount
- Count
- IntegrationLatency
- Latency
- Errors

# Permission

- IAM + Identity-based / resource-based policies
- Cognito authorizer
- Lambda authorizer

# API Types

## HTTP API

- Low-latency, cost-effective
- No usage plans and API keys
- Native support for OIDC and OAuth 2.0

## REST API

- All features above
- No support for OIDC and OAuth 2.0

## Websocket API

- Callback connection URL
  - WebSocketURL/@connections/connectionId
- Routing based on incoming data
