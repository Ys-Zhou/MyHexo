---
title: Security
date: 2019-11-11 09:33:00
tags: AWS - Big Data
---

# AWS KMS (Key Management Service)

- Encryption for AWS services
- Fully integrated with IAM for authorization
- Integration into
  - EBS: encrypt volumes
  - S3: SSE-KMS of objects
  - Redshift / RDS: encrypt data
  - Etc.
- Can use KMS using CLI / SDK

## About KMS
- Keys (CMK) in KMS can never be retrieved by users
- KMS can only encrypt up to 4KB of data per call
  - If data &gt; 4 KB, use client-side encryption
- To give access to KSM to a user
  - Key Policy allows the user
  - IAM Policy allows the API calls

## KMS Detail
- Able to fully manage the keys and policies
  - Create
  - Rotation policies
  - Disable
  - Enable
- Able to audit key usage using CloudTrail
- Three types of Customer Master Keys (CMK)
  - AWS Managed Service Default CMK: free
  - User Keys created in KMS: $1 / month
  - User Keys imported (256-bit symmetric): $1 / month
- Each API call to KMS: $0.03 / 10000 calls
  - Encrypt API
  - Decrypt API

## Encryption Using KMS
- Requires migration (Snapshot / Backup)
  - EBS Volumes
  - RDS databases
  - ElastiCache
  - EFS file system
- In-place encryption
  - S3

# CouldHSM

- HSM = Hardware Security Module
- Dedicated encryption hardware
- Users manage their keys (not AWS)
- FIPS I40-2 Level 3 compliance
- CloudHSM clusters are spread across multi-AZ
- Support both symmetric and asymmetric encryption
- Must use the CloudHSM Client Software

## KSM vs. CloudHSM
Feature|KSM|CloudHSM
---|---|---
Tenancy|Multi-tenant key storage|Single dedicated tenant key storage
Keys|Keys managed by AWS|Customer managed HSM
Encryption|Symmetric encryption only|Symmetric and asymmetric encryption
Cryptographic Acceleration|None|SSL/TLS Acceleration & Oracle TDE Acceleration
Key Storage and Management|Accessible from multiple regions. Centralized management from IAM|Deployed and managed form a customer VPC. Accessible and can be shared across VPCs using VPC peering

# Security in AWS Services

## Kinesis
- Kinesis Data Streams
  - SSL/TLS
  - Server-side encryption using KMS
  - Client-side encryption can only be implemented manually
  - IAM
    - **DynamoDB table when using KCL**
  - VPC Endpoints (Interface Endpoints)
- Kinesis Data Firehose
  - Can encrypt the delivery stream using KMS
  - IAM
    - Deliver to S3, ES, Redshift, Splunk
  - VPC Endpoints (Interface Endpoints)
- Kinesis Data Analytics
  - IAM
    - Sources and destination (Kinesis Data Streams, Kinesis Data Firehose)

## SQS
- SSL/TLS
- Server-side encryption using KMS
- Client-side encryption can only be implemented manually
- IAM
- **SQS queue access policy**
- VPC Endpoints (Interface Endpoints)

## IoT
- IoT policies
  - Attached to X.509 or Cognito Identities
  - Can be attached to groups as well as individual Things
- IAM
  - Used for controlling IoT APIs
  - Attach roles to Rules Engine to allow their actions

## S3
- SSL/TLS
- Server-side encryption
  - SSE-S3, SSE-KMS, SSE-C
- Client-side encryption
  - Such as S3 Encryption Client
- IAM
- S3 bucket policies
- ACLs
- Versioning + MFA when deleting
- VPC Endpoints (Gateway Endpoints)
- CORS for protecting websites hosted on S3
- Vault lock policies to prevent deletes for Glacier

## DynamoDB
- SSL/TLS
- KMS encryption
  - Setting when creating the table
  - To enable or disable encryption, create new table and copy the data
- IAM
  - Access to API / DAX
- VPC Endpoints (Gateway Endpoints)

## RDS
- VPC
  - Can use Security Groups
- SSL/TLS
- KMS encryption
- IAM
- IAM authentication
  - Support Aurora, PostgreSQL and MySQL
  - Manage user permissions within the database itself
- Microsoft SQL Server and Oracle support TDE (Transparent Data Encryption)

## Lambda
- KMS encryption for secrets
  - Can use AWS Systems Manager Parameter Store as well
- IAM
  - Each function need roles
- Can deploy function in VPC

## Glue
- IAM
- KMS encryption
  - Data Catalog, connection passwords
- Encrypt data written by the job
  - To S3: SSE-S3, SSE-KMS
  - To CloudWatch
  - To Job bookmark
- Can enforce SSL on JDBC

## EMR
- SSH using EC2 key pairs
- IAM
- EC2 Security Groups
  - One for master node
  - Another for cluster nodes (core nodes and task nodes)
- Encryption data at-rest
  - EMRFS in S3
    - SSE-S3, SSE-KMS
    - CSE-KMS, CSE-C
    - EMR dose not allow unencrypted data to be stored into S3
  - Local disk
    - Open-source HDFS encryption
    - EBS volume encryption
      - EBS encryption
      - LUKS encryption
- Encryption data in transit
  - SSL/TLS
- Kerberos authentication from Active Directory
- Apache Ranger
  - Centralized Authorization
  - Role Based Access Control
  - Need to setup on external EC2

## ElasticSearch
- VPC
- SSL/TLS
- KMS
- ElasticSearch policy
- IAM or Cognito based authentication
  - Cognito allow end-users to log-in to Kibna through enterprise identity providers such as Microsoft Active Directory using SAML 

## Redshift
- VPC
  - Cluster security group
- Encryption in flight using JDBC driver with SSL
- Encryption at reat using KMS or **HSM**
- IAM
- COPY or UNLOAD command
  - IAM roles need to be referenced or use your own credits
  - COPY from or UNLOAD to S3, support both SSE-S3 and SSE-KMS
 
## Athena
- IAM
- SSL/TLS between Athena and S3 or JDBC
- Data in S3: Every security features in S3
- Can use Glue Catalog security to restrict access

## QuickSight
- Standard edition
  - IAM users
  - Email based accounts
  - Row Level Security Control
- Enterprise edition
  - Active Directory
  - Federated Login
  - MFA
  - Encryption at rest and in SPICE

# STS

- Security Token Service
- To grant limited and temporary access to AWS resources
- Token is valid for up to 1 hour

## Use Cases
- Cross account access
- Federation
  - Active Directory
  - SAML
  - SSO (Single Sign On)
- Federation with third party providers / Cognito
  - Web or mobile application login

## Cross Account Access
- Define an IAM Role for another account ro access
- Define accounts that can access the IAM Role
- Use STS to retrieve credentials and impersonate the IAM Role
- The temporary credentials can be valid between 15 minutes to 1 hour

## Identity Federation
- Let users outside of AWS to assume temporary role for accessing AWS resources
- These users assume identity provided access role
- Federations
  - LDAP
  - SAML
  - Microsoft Active Directory (SAML)
  - SSO
  - Open ID
  - Cognito

### SAML
- To integrate Active Directory or any SAML 2.0
- Provides access to AWS resources without creating IAM user
- If identity provider is not compatible with SAML 2.0
  - Implement an identity broker

### Cognito
- Federated identity pools for public applications

# CloudTrail

- Provide governance, compliance and audit for your AWs Account
- Track every API call made to your account
- CloudTrail is enabled by default
- Can pjut logs form CloudTrail into CloudWatch Logs
- CloudTrail shows the past 90 days of activity
- The default UI only shows *Create*, *Modify*, and *Delete* events
- CloudTrail Trail
  - Can get a detailed list of all the events you choose
  - Ability ot stroe these events in S3 for further analysis
  - Can be region specific or global
- CloudTrail Logs hava SS3-S3 encryption when placed into S3

# VPC Endpoints

- Allow you to connect to AWS Services using private network
- No longer nedd IGW, NAT, etc.
- Gateway type
  - Provision a target and must be used in a route table
  - Only S3 and DynamoDB
- Interface type
  - Provision an ENI as an entry point (need security group)
  - Also called VPC PrivateLink
