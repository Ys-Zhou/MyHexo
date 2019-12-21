---
title: Visualization
date: 2019-11-11 08:38:00
tags: AWS - Big Data
---

# Amazon QuickSight

- Business analytics service
  - Build visualization
  - Perform ad-hoc analysis
  - Quickly get business insights from data
  - Multiple devices (browsers, mobile)
- Serverless

## QuickSight Data Sources
- Redshift
- RDS
- Athena
- On-premise databases
- S3 or on-premise files
  - Excel
  - CSV, TSV
  - Some log format
- Can do simple data transformation in data preparation

## SPICE
- Data sets are imported into SPICE
  - Super-fast, Parallel, In-memory, Calculation, Engine
  - Columnar storage
- Each user gets 10GB of SPICE
- Highly available and scalable

## QuickSight Use Cases
- Interactive ad-hoc exploration / visualization of data
- Create dashboards / stories
- Visualize data from
  - AWS (RDS, Redshift, Athena, S3)
  - On-premise databases
  - Saas applications (Salesforce)
  - Other JDBC / ODBC data sources

## QuickSight Anti-Patterns
- Highly formatted reports
- ETL
  - Use Glue, Apache Spark

## QuickSight Security
- MFA (Multi-Factor Authentication)
- VPC connectivity
  - Add QuickSight's IP address range to security group to connect database
- Private VPC access
  - ENI (Elastic Network Interface)
  - AWS Direct Connect
- RLS (Row-Level Security)

## QuickSight User Management
- Standard Edition
  - IAM or email signup
- Enterprise Edition
  - Microsoft Active Directory

## QuickSight Pricing
- Annual subscription (user / month)
- Extra SPICE capacity (GB / month)
- Month to month (GB / month)
- Enterprise edition
  - Encryption at rest
  - Microsoft Active Directory integration

## QuickSight Visual Types
- AutoGraph
- Bar Charts (histograms)
  - For comparison and distribution
- Line graphs
  - For changes over time
- Scatter plots, heat maps
  - For correlation
- Pie graphs, tree maps
  - For aggregation
- Pivot tables
  - For tabular data
- Stories

# Alternative Visualization Tools
- Web-based
  - D3.js, Chart.js, Highchart.js
- Business Intelligence Tools
  - Tableau, MicroStrategy
