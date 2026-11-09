# Create a Redis Cluster

This guide walks you through creating a new **Redis Cluster** on vDB, including cluster configuration, backup settings, and the option to restore from an existing backup.

***

## Prerequisites

* Your vDB account is activated.
* At least 1 **Backup Policy** and 1 **Backup Location** (status Available, Product = vDB) are configured in Backup Center.
* (Optional — if restoring from backup) At least 1 backup exists in Backup Center with a restore point in **Completed** status.

***

## Step 1 — Go to Create Database

1. Go to [https://vdb.console.greennode.ai](https://vdb.console.greennode.ai).
2. In the left menu, select **Memory Store**.
3. Click **Create Database**.

![](<../../../.gitbook/assets/Redis-list (1).png>)

***

## Step 2 — Basic Configuration

Fill in the general database information:

* **Engine:** Select **Redis**.
* **Cluster Name:** 3–63 characters, lowercase letters, numbers, and hyphens only.
* **Engine Version:** Select the Redis version that suits your needs.
* **Instance Type:** Select the CPU/RAM configuration.
* **Storage Size:** Storage capacity in GB.

***

## Step 3 — Cluster Configuration

In the **Cluster configuration** section, select your **Cluster mode**:

![](<../../../.gitbook/assets/create-redis-cluster (1).png>)

| Option           | Description                                                                                     |
| ---------------- | ----------------------------------------------------------------------------------------------- |
| **Single-node**  | Single master node with optional replicas. Suitable for smaller workloads and dev environments. |
| **Cluster Mode** | Replication across multiple nodes with automatic failover. Suitable for production and HA.      |

Select **Cluster Mode** to proceed with creating a Redis Cluster.

{% hint style="info" %}
When Cluster Mode is enabled, the system supports High Availability with automatic failover. Note that some Redis commands are unavailable in this mode.
{% endhint %}

### Select Sharding Type

After selecting Cluster Mode, choose the **Sharding type**:

* **Non-sharding:** A single shard (node group) with 1 Primary and its Replicas. This is the option available now and is selected by default.
* **Sharding:** Labeled **Coming soon** and cannot be selected yet.

### Configure Number of Nodes

Select the **Number of nodes** (3–10, default 3):

* The total includes **1 Primary (Writer)** and the remaining **Replicas (Reader)**.
* For example: selecting 3 nodes = 1 Primary + 2 Replicas; selecting 4 nodes = 1 Primary + 3 Replicas.

The breakdown next to the field (for example, **1 Primary (Writer) • 2 Replicas (Reader)**) updates as you change the number of nodes.

***

## Step 4 — Restore from Backup (Optional)

If you want to create the cluster from an existing backup instead of an empty database, use the **Backup Image** section:

![](<../../../.gitbook/assets/create-redis-restore (1).png>)

1. Backups from Backup Center are displayed directly as a table (includes both Auto and Manual Backups). Only backups with at least 1 restore point in **Completed** status are shown.
2. Find the backup you want, click the expand icon to view the list of **restore points**.
3. Select the radio button of the restore point you want to restore from.

When a restore point is selected, the system automatically pre-fills the instance settings (instance type, storage size, etc.) from the backup — you do not need to enter these manually.

{% hint style="warning" %}
The **Storage Size** of the new cluster must be **greater than or equal to the Min.Restore Size (GB)** of the selected restore point. Otherwise, the system will return an error and prevent cluster creation.
{% endhint %}

If no restore point is selected, the cluster will be created with an empty database.

***

## Step 5 — Backup Settings

In the **Backup Settings** step, configure data protection for the cluster:

![](<../../../.gitbook/assets/redis-backup-setting (1).png>)

| Field               | Required | Description                                                                                          |
| ------------------- | -------- | ---------------------------------------------------------------------------------------------------- |
| **Backup Policy**   | Yes      | Select a policy that defines the backup schedule and retention period from Backup Center.            |
| **Backup Location** | Yes      | Select a backup storage location (only locations with status Available and Product = vDB are shown). |

{% hint style="warning" %}
* Both fields are required — this step cannot be skipped when creating in Cluster Mode.
* After the cluster is created, **the Backup Location cannot be changed**.
* If no Backup Location is available, create one in Backup Center before proceeding.
{% endhint %}

Use the **refresh** icon next to each dropdown to reload the latest list from Backup Center.

***

## Step 6 — Review and Create

Review all configuration on the **Review** page, then click **Create Database**.

The system will provision the cluster with:

* 1 Master node + the number of Replicas you configured.
* Auto Backup running on the schedule of the configured Backup Policy.
* Data from the selected restore point (if chosen in Step 4).

After successful creation, you will be redirected to the cluster detail page.

***

## Next Steps

* [View Topology and change the number of Replicas](manage-redis-cluster.md)
* [Create a Manual Backup or view backup history](manage-redis-cluster.md)
* [View limitations and constraints for Redis Cluster](https://github.com/vngcloud/docs/blob/main/English/vdb/memorystore-database-service-mds/redis-cluster/redis-cluster-limitations.md)
