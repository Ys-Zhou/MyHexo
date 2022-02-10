---
title: DVA - CloudFront
date: 2022-02-09 16:29:28
tags: AWS - Developer
---

# Geo Restriction

- CloudFront can set geo restriction without using WAF
  - You can use WAF as well but it is unnecessary

# Caching

- Cache based on
  - Headers
  - Session Cookies
  - Query String Parameters
- You can set TTL for a cache as well as invalidate it manually
  - Call `CreateInvalidation` API
- Maximize cache hits by separating static and dynamic distributions

# Security

- View Protocol Policy
  - Redirect HTTP to HTTPS
  - HTTPS only
- Origin Protocol Policy
  - HTTPS only
  - Match Viewer (use the same protocol as viewer)
- S3 Website dose not support HTTPS, so use CloudFront to force HTTPS will be an option

# Signed URL / Cookie

- Signed URL / Cookie policy includes
  - Expiration
  - User IP ranges
  - Trusted signers (which AWS accounts can sign)
- Create Signed URLs / Cookies
  - Create a key pair
  - Upload the public key to CloudFront
  - Add the public key to a key group
  - Use the private key in your application to create Signed URLs / Cookies

# Price Classes

- Price Class All: all regions - best performance
- Price Class 200: excludes the most expensive regions
- Price Class 100: only the least expensive regions

# Multi-Origin

- You can set Cache Behaviors to redirect request to different origins

# Origin Groups

- You can set a origin group as your distribution origin
  - Includes a primary and a secondary origin (as fail-over)
  - You can combine Origin Group with S3 Replication to build HA architecture

# Field Level Encryption

- Specify set of fields in POST requests that you want to be encrypted (up to 10 fields)
- Specify the public key to encrypt them
- The fields will be encrypted at Edge Location, and your applications can use the private key to decrypted them
