---
title: DVA - ElastiCache
date: 2022-02-09 16:30:59
tags: AWS - Developer
---

# Precondition of Data caching

- Data is safe to be cached
  - Out-of-date data is acceptable (eventually consistent)
- Data is caching effective
  - Data changing slowly
  - Few data is frequently needed
- Data is structured well for caching
  - Key-value data
  - Aggregation results

# ElastiCache Strategies

## Lazy Loading / Cache-aside / Lazy Population

- Write
  - Write to DB
- Read
  - Read from cache if hit
  - Read from DB if miss, and write to cache
- Pros
  - Only requested data is cached (cache will not full with unused data)
  - Node failures are not fatal (only increase latency)
- Cons
  - Cache miss results in 3 round trips (noticeable delay)
  - May retrieve stale data (data was updated in DB but outdated in cache)

## Write Through

- Write
  - Write to DB as well as cache
- Read
  - Read from cache if hit
  - Read from DB if miss
- Pros
  - Data is cache is never stale
  - Cache miss results in only 2 round trips
- Cons
  - Every write requires 2 calls
  - Keep missing until it is write to DB
  - Cache churn (massive data in cache will never be used)

## Cache Evictions and TTL

- Cache eviction ca occur
  - Be deleted explicitly
  - The memory is full and it is not recently used
    - If too many evictions happen due to memory, scale up (out) the cache
  - A TTL is set to the cache 

## Strategies Consideration

- Lazy loading is mostly better, especially on the read side
- Write-through is usually combined with Lazy Loading
- Setting TTL is generally good, except when you are using write-through. A sensible TTL is important.

# ElastiCache for Redis Replication

- Cluster Mode Disable
  - One primary node, 0~5 read-only replica nodes (async replication)
- Cluster Mode Enable
  - Data is partitioned across shards
  - Each shard has one primary node and 0~5 replica nodes
  - Up to 500 nodes per cluster
