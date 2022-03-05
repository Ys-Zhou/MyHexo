---
title: 'Developer: EBS'
date: 2022-02-19 20:01:44
tags: AWS - Developer
---

# EBS Volume Types

- gp2: General purpose SSD
  - 1 GiB ~ 16 TiB
  - Can burst to 3000 IOPS for small size volumes
  - 3 IOPS per GB, up to 16000 IOPS
- gp3: General purpose SSD
  - 1 GiB ~ 16 TiB
  - Baseline of 3000 IOPS and throughput of 125 MiB/s
  - Up to 16000 IOPS and 1000 MiB/s
  - Provisioned IOPS and throughput
- io1 / io2: High-performance SSD
  - 4 GiB ~ 16 TiB
  - Up to 64000 Provisioned IOPS for Nitro EC2 instances
  - Up to 32000 Provisioned IOPS for other EC2 instances
  - io2 have more durability and more IOPS per GiB at the same price as io1 (io2 is always better than io1)
- io2 Block Express
  - 4 GiB ~ 64 TiB
  - Up to 256000 Provisioned IOPS
  - Sub-millisecond latency
- st1: Throughput optimized HDD
  - 125 MiB ~ 16 TiB
  - Up to 500 IOPS and 500 MiB/s throughput
- sc1: Cold HDD
  - 125 MiB ~ 16 TiB
  - Up to 250 IOPS and 250 MiB/s throughput

## Other key points

- Only gp2/3 and io1/2 (only SSD) can be used as boot volumes
- Only io1/2 supports EBS multi-attach
