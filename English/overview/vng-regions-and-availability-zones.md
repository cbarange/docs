---
description: Information about GreenNode Regions and Availability Zones
---

# GreenNode Regions and Availability Zones

## Overview

### What is a Region?

A Region is an independent geographic area where GreenNode deploys its infrastructure. Each region is designed to operate independently, ensuring your data is stored in the location you choose.

### What is an Availability Zone (AZ)?

An Availability Zone is one or more discrete data centers within a region, equipped with independent power, networking, and connectivity. Deploying applications across multiple AZs increases availability and fault tolerance.

### Naming Convention

* **Region**: Named by geographic location (e.g., HCM - Ho Chi Minh, HAN - Ha Noi, BKK - Bangkok)
* **AZ**: Combines region name + sequence number + letter (e.g., HCM-1A, HCM-1B, HAN-1A, BKK-1A)

***

## VNG Regions List

| Region Code | Region Name | Availability Zones             | Console URL                                                                                                                        |
| ----------- | ----------- | ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| HCM         | Ho Chi Minh | HCM-1A, HCM-1B, HCM-1C, BKK-1A | https://hcm-3.console.greennode.ai                                                                                                  |
| HAN         | Ha Noi      | HAN-1A                         | [https://han-1.console.greennode.ai/vserver/v-server/cloud-server](https://han-1.console.greennode.ai/vserver/v-server/cloud-server) |

{% hint style="info" %}
As of December 15, 2025, GreenNode supports 2 regions: Ho Chi Minh and Ha Noi.
{% endhint %}

***

## Service Availability by Region

{% hint style="info" %}
**Legend:**

* x : Service available in this zone
* \- : Service not available in this zone
* Coming Soon: Service will be supported soon
{% endhint %}

{% hint style="warning" %}
Service URLs within the same region are identical.
{% endhint %}

***

{% tabs %}
{% tab title="vServer" %}
**vServer**

| Service                             | Global | HCM-1A | HCM-1B | HCM-1C | BKK-1A | HAN-1A | Console URL                                                                                                                                                                                                  |
| ----------------------------------- | :----: | :----: | :----: | :----: | :----: | :----: | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Server**                          |    -   |    x   |    x   |    x   |    x   |    x   | [HCM](https://hcm-3.console.greennode.ai/vserver/v-server/cloud-server) \| [HAN](https://han-1.console.greennode.ai/vserver/v-server/cloud-server)                                                             |
| **Volume**                          |    -   |    x   |    x   |    x   |    x   |    x   | [HCM](https://hcm-3.console.greennode.ai/vserver/block-store/volumes) \| [HAN](https://han-1.console.greennode.ai/vserver/block-store/volumes)                                                                 |
| **Image**                           |    -   |    x   |    x   |    x   |    x   |    x   | [HCM](https://hcm-3.console.greennode.ai/vserver/block-store/images) \| [HAN](https://han-1.console.greennode.ai/vserver/block-store/images)                                                                   |
| **Snapshot**                        |    -   |    x   |    x   |    x   |    -   |    x   | [HCM](https://hcm-3.console.greennode.ai/vserver/block-store/snapshot/overview) \| [HAN](https://han-1.console.greennode.ai/vserver/block-store/snapshot/overview)                                             |
| **Network Interface**               |    -   |    x   |    x   |    x   |    x   |    x   | [HCM](https://hcm-3.console.greennode.ai/vserver/network/external-interface/external-interface-group) \| [HAN](https://han-1.console.greennode.ai/vserver/network/external-interface/external-interface-group) |
| **VPC, DHCP, VIP, Peering**         |    -   |    x   |    x   |    x   |    x   |    -   | [HCM](https://hcm-3.console.greennode.ai/vserver/network/vpc)                                                                                                                                                 |
| **Bandwidth**                       |    -   |    x   |    x   |    x   |    -   |    x   | [HCM](https://hcm-3.console.greennode.ai/vserver/network/bandwidth/list) \| [HAN](https://han-1.console.greennode.ai/vserver/network/bandwidth/list)                                                           |
| **vLB (Load Balancer)**             |    -   |    x   |    x   |    x   |    x   |    x   | [HCM](https://hcm-3.console.greennode.ai/vserver/load-balancer/vlb) \| [HAN](https://han-1.console.greennode.ai/vserver/load-balancer/vlb)                                                                     |
| **GLB (Global Load Balancer)**      |    x   |    -   |    x   |    -   |    -   |    -   | [Portal](https://glb.console.greennode.ai/overview)                                                                                                                                                           |
| **vDCI (Dedicated Cloud Instance)** |    x   |    -   |    -   |    -   |    -   |    -   | [Portal](https://vdci.console.greennode.ai)                                                                                                                                                                   |
{% endtab %}

{% tab title="vNetwork" %}
**vNetwork**

| Service                               | Global | HCM-1A | HCM-1B | HCM-1C | BKK-1A | HAN-1A | Console URL                                                                                                      |
| ------------------------------------- | :----: | :----: | :----: | :----: | :----: | :----: | ---------------------------------------------------------------------------------------------------------------- |
| **Endpoint, NAT, VPN, Cross Connect** |    -   |    x   |    x   |    x   |    -   |    x   | [HCM](https://hcm-3-vnetwork.console.greennode.ai/overview) \| [HAN](https://han-1-vnetwork.console.greennode.ai/) |
| **vDNS**                              |    x   |    -   |    x   |    x   |    -   |    -   | [Portal](https://vdns.console.greennode.ai/hosted-zones)                                                          |
| **Global View**                       |    x   |    -   |    x   |    x   |    x   |    -   | [Portal](https://regionview.console.greennode.ai/resource-region)                                                 |
{% endtab %}

{% tab title="vStorage" %}
**vStorage**

| Service          | Global | HCM-1A | HCM-1B | HCM-1C | BKK-1A | HAN-1A | Console URL                                                                                                          |
| ---------------- | :----: | :----: | :----: | :----: | :----: | :----: | -------------------------------------------------------------------------------------------------------------------- |
| **Storage**      |    -   |    x   |    -   |    -   |    -   |    x   | [HCM](https://vstorage.console.greennode.ai/storage/list) \| [HAN](https://vstorage.console.greennode.ai/storage/list) |
| **File Storage** |    -   |    x   |    x   |    x   |    -   |    -   | [HCM](https://efs.console.greennode.ai/overview)                                                                      |
| **Data Sync**    |    x   |    -   |    -   |    -   |    -   |    -   | [Portal](https://datasync.console.greennode.ai/overview)                                                              |
{% endtab %}

{% tab title="vDB" %}
**vDB (Database Service)**

| Service                 | Global | HCM-1A | HCM-1B | HCM-1C | BKK-1A | HAN-1A | Console URL                                                    |
| ----------------------- | :----: | :----: | :----: | :----: | :----: | :----: | -------------------------------------------------------------- |
| **Relational Database** |    -   |    x   |    x   |    x   |    -   |    -   | [Portal](https://vdb.console.greennode.ai/relational/database)  |
| **MemoryStore**         |    -   |    x   |    x   |    x   |    -   |    -   | [Portal](https://vdb.console.greennode.ai/memorystore/database) |
| **Kafka**               |    -   |    x   |    -   |    -   |    -   |    -   | [Portal](https://vdb.console.greennode.ai/kafka/cluster)        |
| **OpenSearch**          |    -   |    x   |    -   |    -   |    -   |    -   | [Portal](https://vdb.console.greennode.ai/opensearch/cluster)   |
{% endtab %}

{% tab title="VKS" %}
**VKS (VNG Kubernetes Service)**

| Service                | Global | HCM-1A | HCM-1B | HCM-1C | BKK-1A | HAN-1A | Console URL                                                                                              |
| ---------------------- | :----: | :----: | :----: | :----: | :----: | :----: | -------------------------------------------------------------------------------------------------------- |
| **Kubernetes Cluster** |    -   |    x   |    x   |    x   |    x   |    x   | [HCM](https://vks.console.greennode.ai/overview) \| [HAN](https://vks-han-1.console.greennode.ai/overview) |
| **Container Registry** |    -   |    x   |    x   |    x   |    -   |    x   | [HCM](https://vcr.console.greennode.ai/repository/list) \| [HAN](https://han-1.console.greennode.ai/vcr)   |
{% endtab %}

{% tab title="Backup Center" %}
**Backup Center**

| Service                    | Global | HCM-1A | HCM-1B | HCM-1C | BKK-1A | HAN-1A | Console URL                                                                                                                              |
| -------------------------- | :----: | :----: | :----: | :----: | :----: | :----: | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **Backup Center**          |    -   |    x   |    x   |    x   |    -   |    x   | [HCM](https://backupcenter.console.greennode.ai/backup-server/list) \| [HAN](https://backupcenter.console.greennode.ai/backup-server/list) |
| **Server Migration**       |    -   |    x   |    x   |    x   |    x   |    x   | [HCM](https://backupcenter.console.greennode.ai/server-migration) \| [HAN](https://backupcenter.console.greennode.ai/server-migration)     |
| **Disaster Recovery (DR)** |    x   |    x   |    x   |    x   |    -   |    -   | [Portal](https://backupcenter.console.greennode.ai/protected-server/list)                                                                 |
{% endtab %}

{% tab title="vMonitor" %}
**vMonitor Platform**

| Service            | Global | HCM-1A | HCM-1B | HCM-1C | BKK-1A | HAN-1A | Console URL                                                            |
| ------------------ | :----: | :----: | :----: | :----: | :----: | :----: | ---------------------------------------------------------------------- |
| **Metric**         |    x   |    -   |    -   |    -   |    -   |    -   | [Portal](https://vmonitor.console.greennode.ai/quota-usages/metric)     |
| **Log**            |    x   |    -   |    -   |    -   |    -   |    -   | [Portal](https://vmonitor.console.greennode.ai/log/project)             |
| **Synthetic Test** |    x   |    -   |    -   |    -   |    -   |    -   | [Portal](https://vmonitor.console.greennode.ai/synthetic-test/api-test) |
| **Notification**   |    x   |    -   |    -   |    -   |    -   |    -   | [Portal](https://vmonitor.console.greennode.ai/notification)            |
{% endtab %}

{% tab title="vCDN" %}
**vCDN**

| Service             | Global | HCM-1A | HCM-1B | HCM-1C | BKK-1A | HAN-1A | Console URL                                               |
| ------------------- | :----: | :----: | :----: | :----: | :----: | :----: | --------------------------------------------------------- |
| **Web Accelerator** |    x   |    -   |    -   |    -   |    -   |    -   | [Portal](https://vcdn.vngcloud.vn/webacc/list.html)       |
| **Object Download** |    x   |    -   |    -   |    -   |    -   |    -   | [Portal](https://vcdn.vngcloud.vn/obj-download/list.html) |
| **Video On Demand** |    x   |    -   |    -   |    -   |    -   |    -   | [Portal](https://vcdn.vngcloud.vn/vod/list.html)          |
| **Live Streaming**  |    x   |    -   |    -   |    -   |    -   |    -   | [Portal](https://vcdn.vngcloud.vn/live-cdn/list.html)     |
{% endtab %}

{% tab title="Security & IAM" %}
**Security & IAM**

| Service | Global | HCM-1A | HCM-1B | HCM-1C | BKK-1A | HAN-1A | Console URL                                   |
| ------- | :----: | :----: | :----: | :----: | :----: | :----: | --------------------------------------------- |
| **IAM** |    x   |    -   |    -   |    -   |    -   |    -   | [Portal](https://iam.console.greennode.ai/)    |
| **KMS** |    -   |    -   |    -   |    -   |    -   |    x   | [HAN](https://han-1-kms.console.greennode.ai/) |
{% endtab %}

{% tab title="Marketplace & AI" %}
**Marketplace & AI**

| Service          | Global | HCM-1A |    HCM-1B   |    HCM-1C   | BKK-1A | HAN-1A | Console URL                                                                                                              |
| ---------------- | :----: | :----: | :---------: | :---------: | :----: | :----: | ------------------------------------------------------------------------------------------------------------------------ |
| **vMarketplace** |    -   |    x   |      x      |      x      |    -   |    x   | [HCM](https://marketplace.console.greennode.ai/overview) \| [HAN](https://marketplace-han-1.console.greennode.ai/overview) |
| **vColo**        |    x   |    -   |      -      |      -      |    -   |    -   | [Portal](https://vcolo.console.greennode.ai/overview)                                                                     |
| **AI Platform**  |    -   |    x   | Coming Soon | Coming Soon |    -   |    -   | [Portal](https://aiplatform.console.greennode.ai/overview)                                                                |
| **MaaS**         |    x   |    -   |      -      |      -      |    -   |    -   | [Portal](https://aiplatform.console.greennode.ai/models)                                                                  |
{% endtab %}
{% endtabs %}

***

## Considerations When Choosing Region and AZ

1. **Latency**: Choose a region close to your end users to reduce latency
2. **Data Compliance**: Some regulations require data to be stored in a specific geographic area
3. **High Availability**: Deploy applications across multiple AZs to ensure high availability
4. **Cost**: Service pricing may vary between regions
