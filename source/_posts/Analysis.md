---
title: Analysis
date: 2019-10-30 08:50:00
tags: AWS - Big Data
---

# Kinesis Analytics

- Querying streams of data continuously
- Can receive data from and send results to
  - Kinesis Data Streams
  - Kinesis Data Firehose
- Analysis using SQL
- Can use reference table from S3 to help analysis
- Errors will be output to Error Stream

## Use-cases
- Streaming ETL
- Continuous metric generation
- Responsive analytics

## Schema discovery
- Generate data schema automatically by feeding some stream data

## RANDOM_CUT_FOREST
- A SQL function offered by Kinesis Data Analytics
- For anomaly detection
