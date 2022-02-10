---
title: DVA - S3
date: 2022-02-09 16:30:12
tags: AWS - Developer
---

# MFA Delete

- You must enable Versioning on S3 bucket before enabling MFA-Delete
- MFA-Delete can only be enabled / disabled / used through CLI
- Only bucket owner can enable / disable MFA-Delete
- MFA-Delete will be applied to
  - Permanently delete an object version
  - Suspend versioning on the bucket
- MFA-Delete will not be applied to
  - Mark an object deleted
  - Enable versioning
  - List object including deleted versions

# Force Encryption

- Use Bucket Policy
  - Deny upload action when the object is unencrypted
- Default Encryption
  - It will be encrypted by default when you upload an unencrypted object

# Access Logs (Server Access Logging)

- Any request made to the S3 bucket will be logged into another S3 bucket
- Do not enable Server Access Logging on a logging bucket (result in logging loop)
- By enabling server access logging, your bucket ACL will be updated automatically to include access to the S3 log delivery group

# Replication

- Must enable versioning in both source and destination buckets
- Can cross-account & cross-region
- After activating, only new objects are replicated
- Can optional set it to replicate delete markers
- Deletions with a version ID are not replication
- Replication is not chained
  - The destination bucket will not replicate replicated objects to another bucket

# Pre-signed URLs

- Can generate pre-signed URLs using SDK or CLI
  - CLI or SDK for downloads
  - Only SDK for uploads
- Use `--expires-in` parameters to set expire time

# Glacier Restore Time

- Glacier
  - Expedited - 1 ~ 5 minutes
  - standard - 3 ~ 5 hours
  - Bulk - 5 ~ 12 hours
- Glacier Deep Archive
  - Standard - 12 hours
  - Bulk - 48 hours

# Lifecycle Rules

- Transition actions
- Expiration actions
  - Can be used to delete old versions
  - Can be used to delete incomplete multi-part uploads
- Rules can be created for
  - a certain prefix
  - certain object tags

# Performance

- 100 ~ 200 ms latency
- 3500 PUT/COPY/POST/DELETE and 5500 GET/HEAD req/s per prefix
- KMS limitation if you use SSE-KMS
  - Call `GenerateDataKey` API when upload
  - Call `Decrypt` API when download
  - KMS has a quota 5500 or 10000 or 30000 req/s based on region
  - You can request a quota increase
