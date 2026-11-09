# Redis Cluster

**Redis Cluster** on vDB is a Redis deployment with a **Non-sharding** architecture consisting of 1 Master node and 2 to 9 Replica nodes (3–10 nodes total), with automatic failover and vBackup integration for comprehensive data protection.

***

## Architecture

Redis Cluster on vDB follows a **1 Master – N Replicas** model:

* **Master node:** Handles all read and write operations.
* **Replica nodes (2–9):** Asynchronously replicate data from the Master, serve read operations, and are ready for failover.
* **Automatic Failover:** When the Master fails, a Replica is automatically promoted to become the new Master. The cluster continues to operate after failover completes.

```
┌─────────────┐
│   Master    │  ← Read / Write
└──────┬──────┘
       │ Async Replication
  ┌────┴────┐
  ▼         ▼
Replica 1  Replica 2 ...  ← Read / Failover
```

{% hint style="info" %}
**Scope:** Redis Cluster on vDB currently supports **Non-sharding** mode (single shard only). Multi-shard sharding will be developed in a future release.
{% endhint %}

***

## Single-node vs Cluster Comparison

| Feature            | Single-node                   | Cluster (Non-sharding)            |
| ------------------ | ----------------------------- | --------------------------------- |
| Masters            | 1                             | 1                                 |
| Max Replicas       | 5                             | 9                                 |
| Automatic Failover | No                            | Yes                               |
| Read Scaling       | Yes (via replicas)            | Yes (via replicas)                |
| Write Scaling      | No (single writer)            | No (single writer)                |
| Backup Integration | No                            | Yes (vBackup — Policy + Location) |
| Best for           | Dev, staging, small workloads | Production, High Availability     |

***

## Scaling Replicas

You can change the number of Replicas in a Cluster after creation with **no downtime**. The system performs scaling step by step:

* **Scale up:** Adds one replica at a time, waiting for each replica to complete its initial sync before adding the next.
* **Scale down:** Removes replicas in descending index order, waiting for each step to complete before proceeding.

{% hint style="warning" %}
Each change can only increase or decrease by **1 node** at a time (current−1 or current+1), and the cluster must keep between **3 and 10 nodes**. Jumping multiple steps in a single action is not allowed. This ensures each sync completes before the next step, preventing replication errors during abrupt scaling.
{% endhint %}

***

## Backup Integration (vBackup)

Redis Cluster has built-in integration with **vBackup** to protect your data:

* **Auto Backup:** Runs on the schedule defined by the Backup Policy selected during cluster creation.
* **Manual Backup:** Create a Full Snapshot on demand at any time from the cluster detail page.
* **Restore:** Create a new cluster from an existing restore point in Backup Center.

| Detail              | Value                                        |
| ------------------- | -------------------------------------------- |
| Backup type         | Full Snapshot (Incremental not supported)    |
| Storage             | vStorage (S3-compatible) with Object Lock    |
| Concurrency limit   | 1 backup job at a time                       |
| When quota exceeded | Returns `QuotaExceeded` error, no auto-retry |

***

## Next Steps

| I want to...                             | Go to...                                                                                                                                          |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Create a new Redis Cluster               | [Create a Redis Cluster](create-redis-cluster.md)                                                                                                 |
| Manage topology, backups, delete cluster | [Manage a Redis Cluster](manage-redis-cluster.md)                                                                                                 |
| View limitations and constraints         | [Limitations](https://github.com/vngcloud/docs/blob/main/English/vdb/memorystore-database-service-mds/redis-cluster/redis-cluster-limitations.md) |
