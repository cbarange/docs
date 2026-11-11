# Release notes

## Nov 11, 2026

VKS (VNG Kubernetes Engine) has just released a new update with the following improvements:

**New features:**

* **Configure vLB listener ACLs via annotations:** Starting with version **0.4.2** of GreenNode LoadBalancer Controller, you can configure the ACL of a Load Balancer listener directly in your manifest (Service for NLB, Ingress for ALB) — no vLB Portal needed.
  * New annotations **`vks.vngcloud.vn/dropped-cidrs`** (comma-separated list of blocked IPv4 CIDRs/IPs, `0.0.0.0/0` = block all) and **`vks.vngcloud.vn/acl-default-action`** (`accept`/`drop` for IPs that match no rule).
  * The existing `vks.vngcloud.vn/inbound-cidrs` annotation now works as a true whitelist: without `acl-default-action`, the default action is **drop**.
  * Removing an annotation restores the listener to its previous ACL value; ACLs configured manually on the Portal are never overwritten.
  * See [Integrate with Network Load Balancer](../network/working-with-nlb/integrate-with-network-load-balancer.md) and [Configure for a Network Load Balancer](../network/working-with-nlb/cau-hinh-cho-mot-network-load-balancer.md) for details.

## Sep 28, 2026 <a href="#sep_07_2026" id="sep_07_2026"></a>

VKS (VNG Kubernetes Engine) has just released a new update with the following improvements:

**New features:**

* **Control Plane Logging:** Collect the Control Plane logs of a Cluster — including **Audit**, **API Server**, **Controller Manager**, **Scheduler** — and send them to your **OpenSearch** or **Kafka** on vDB, for audit, compliance, and troubleshooting. Officially supported in the HCM region.
  * See [Configure Cluster Logging](../monitoring/configure-cluster-logging.md) for details.

## Sep 07, 2026 <a href="#sep_07_2026" id="sep_07_2026"></a>

VKS (VNG Kubernetes Engine) has just released a new update with the following improvements:

**New features:**

* **Control Plane Logging:** Collect the Control Plane logs of a Cluster — including **Audit**, **API Server**, **Controller Manager**, **Scheduler** — and send them to your **OpenSearch** or **Kafka** on vDB, for audit, compliance, and troubleshooting.
  * Enable it while creating a Cluster, or toggle it at any time from the Cluster detail screen.
  * Currently available in **region HAN** only.
  * See [Configure Cluster Logging](../monitoring/configure-cluster-logging.md) for details.

***

## Aug 10, 2026 <a href="#jul_13_2026" id="jul_13_2026"></a>

VKS (VNG Kubernetes Engine) has just released a new update with the following improvements:

**New features:**

* **VKS MCP Server:** Connect AI assistants (Claude, Cursor, VS Code…) straight to VKS over the [Model Context Protocol](https://modelcontextprotocol.io) — manage **Clusters**, **Node Groups** and the **Kubernetes resources inside a cluster** in plain language.
  * **Remote (hosted):** use the hosted endpoint right away and sign in with your **GreenNode IAM user** in the browser (OAuth 2.1) — nothing to install, no secret in any config file. See [Configure Remote MCP](../vks-mcp-server/configure-remote-mcp.md).
  * **Local (stdio):** run the server on your own machine, sharing the `~/.greennode` credentials with the GreenNode CLI. See [Configure Local MCP](../vks-mcp-server/configure-local-mcp.md).
  * See [VKS MCP Server](../vks-mcp-server/) for details.

## Jul 13, 2026 <a href="#jul_13_2026" id="jul_13_2026"></a>

VKS (VNG Kubernetes Engine) has just released a new update with the following improvements:

**New features:**

* **GreenNode CLI for VKS:** Manage the full lifecycle of **Clusters** and **Node Groups** directly from your terminal with the `grn` command — ideal for fast, repeatable operations and automation scripts.
  * See [Manage VKS with the GreenNode CLI](../getting-started/manage-vks-with-the-greennode-cli.md) for details.

***

## Jun 29, 2026 <a href="#jun_29_2026" id="jun_29_2026"></a>

VKS (VNG Kubernetes Engine) has just released a new update with the following improvements:

**New Features:**

* **Kubernetes 1.31 and 1.32 Support:** VKS officially supports Kubernetes versions **1.31** and **1.32**, allowing you to create new clusters or upgrade existing clusters to the latest versions.
  * Brings improvements in stability, security, performance, and compatibility with the Kubernetes ecosystem.
  * Helps you keep your environments aligned with community-supported Kubernetes versions.
  * See [Upgrade Kubernetes Version](../upgrade-kubernetes-version/) for upgrade instructions.

***

## Jun 10, 2026 <a href="#may_21_2026" id="may_21_2026"></a>

VKS (VNG Kubernetes Engine) has just released a new update with the following improvements:

**New features:**

* **Resource Tag** lets you manage a set of key/value labels across all resources — including Servers and Volumes — that belong to a Node Group.
  * Learn more at [Resource Tag](../node-groups/resource-tag.md).
  * VKS API Docs: [VKS API](https://docs.api.greennode.ai/service-docs/vks-api.html).
  * Terraform documentation for creating a node group as Infrastructure as Code: [VKS Terraform](https://registry.terraform.io/providers/vngcloud/vngcloud/latest/docs/resources/vks_cluster_node_group).

***

## May 21, 2026 <a href="#may_21_2026" id="may_21_2026"></a>

VKS (VNG Kubernetes Engine) has just released its latest update with the following improvements:

**New Features:**

* **Cluster-level Auto Healing:** VKS now supports configuring Auto Healing directly at the cluster level — giving you fine-grained control over the unhealthy node detection threshold, safe replacement limits, and self-recovery behavior tailored to your workload type. When a node remains unhealthy long enough, the system automatically deletes the faulty node and provisions a replacement — no manual intervention required.
  * Flexible enable/disable; configure **Timeout**, **Max Unhealthy**, and **Unhealthy Range** via the Portal.
  * Built-in cluster protection: automatically halts replacements when the number of faulty nodes exceeds the threshold — preventing mass deletion during widespread infrastructure incidents.
  * Configuration changes take effect immediately — no cluster or node restart required.
  * See [Auto Healing](../clusters/configure-auto-healing.md) for detailed instructions.
  * API documentation for creating and managing Auto Healing via API: [VKS API](https://docs.api.greennode.ai/service-docs/vks-api.html).
  * Terraform documentation for deploying Auto Healing with Infrastructure as Code: [VKS Terraform](https://registry.terraform.io/providers/vngcloud/vngcloud/latest/docs/resources/vks_cluster).

***

## May 13, 2026 <a href="#may_13_2026" id="may_13_2026"></a>

VKS (VNG Kubernetes Engine) has just released its latest update, bringing a notable new feature to users. Below are the key highlights of this release:

**New Features:**

* **Usage & Limits:** The VKS Portal now includes a **Usage & Limits** page, giving you a clear view of your current resource usage (Kubernetes Clusters used vs. limit) and configuration limits (Node Groups per Cluster, Nodes per Node Group) — all in a read-only interface. Two useful quick links are integrated:
  * **Request limit increase** — submit a limit increase request directly to the 24/7 support team at [GreenNode Helpdesk](https://helpdesk.greennode.ai/portal/en/home).
  * **View resource limits on vServer ↗** — view the full set of resource limits (vCPU, RAM, Disk, etc.) at [vServer Limits](https://hcm-3.console.greennode.ai/vserver/limit).
  * See [Usage & Limits](../usage-limits.md) for detailed instructions.

***

## Apr 15, 2026 <a href="#apr_15_2026" id="apr_15_2026"></a>

VKS (VNG Kubernetes Engine) has just released its latest update, bringing a notable new feature to users. Below are the key highlights of this release:

**New Features:**

* **Kubeconfig with Flexible Certificate Validity:** VKS now allows you to actively choose the certificate validity period when downloading kubeconfig, providing better security control. Supported validity options: **30 days**, **90 days**, **365 days** — suitable for different environments (testing, production, long-term). The system will automatically notify you when the certificate is about to expire and supports flexible renewal.
  * View the certificate expiry date directly on the Portal before downloading.
  * Automatic certificate renewal when eligible; manual renewal supported when needed.
  * For detailed instructions, see [Kubeconfig](../clusters/kubeconfig.md).
  * API documentation for creating and managing clusters via API: [VKS API](https://docs.api.greennode.ai/service-docs/vks-api.html).
  * Terraform documentation for deploying clusters with Infrastructure as Code: [VKS Terraform](https://registry.terraform.io/providers/vngcloud/vngcloud/latest/docs/resources/vks_cluster).

***

## Mar 10, 2026 <a href="#mar_10_2026" id="mar_10_2026"></a>

VKS (VNG Kubernetes Engine) has just released its latest update, bringing a major new feature to users. Below are the key highlights of this release:

**New Features:**

* **Multi-AZ Control Plane:** VKS now officially supports deploying Kubernetes Clusters with the Control Plane distributed across **multiple Availability Zones (AZs)**, ensuring **High Availability** for your cluster. If one AZ experiences an outage, the Control Plane continues to operate normally thanks to redundant instances in other AZs. This feature can be combined with both **Public Cluster** and **Private Cluster**.
  * **Free of charge** during the initial release period. Official pricing will be announced at a later date.
  * Learn about the concept and management at [Multi-AZ Control Plane](../clusters/multi-az-control-plane.md).
  * Step-by-step creation guide at [Create a Multi-AZ Cluster](../getting-started/create-a-multi-az-cluster.md).
  * API documentation for creating and managing clusters via API: [VKS API](https://docs.api.greennode.ai/service-docs/vks-api.html).
  * Terraform documentation for deploying clusters with Infrastructure as Code: [VKS Terraform](https://registry.terraform.io/providers/vngcloud/vngcloud/latest/docs/resources/vks_cluster).

***

## Feb 24, 2026 <a href="#feb_24_2026" id="feb_24_2026"></a>

VKS (VNG Kubernetes Engine) has just released its latest update, bringing several new upgrades to users. Below are the key highlights of this release:

**New Upgrades:**

* **Terraform Provider for VNG Cloud v1.3.8:** Fixed an issue where node group labels and taints were missing after terraform import. Node groups imported from the Portal now have labels and taints correctly reflected in Terraform state. For more information, see [here](https://registry.terraform.io/providers/vngcloud/vngcloud/1.3.8/docs/resources/vks_cluster_node_group).

***

## Feb 4, 2026 <a href="#feb_4_2026" id="feb_4_2026"></a>

VKS (VNG Kubernetes Engine) has just released its latest update, bringing several new upgrades to users. Below are the key highlights of this release:

**New Upgrades:**

* **Terraform Provider for VNG Cloud v1.3.7:** Updated the cluster creation workflow to use the POC provisioning API. For more information, see [here](https://registry.terraform.io/providers/vngcloud/vngcloud/1.3.7/docs/resources/vks_cluster_node_group).

***

## Nov 10, 2025 <a href="#nov_10_2025" id="nov_10_2025"></a>

VKS (VNG Kubernetes Engine) has just released its latest update, bringing several important enhancements to users. Below are the key highlights of this release:

**Improvements:**

* **Deprecation of Kubernetes version 1.28:** GreenNode will discontinue support for Kubernetes version 1.28. Starting from November 10, 2025, users will no longer be able to create new Kubernetes clusters using version 1.28 via the Portal, API, or Terraform. On November 24, 2025, all existing Kubernetes 1.28 clusters will be automatically upgraded to the next supported version.

If you are still running this version, please manually upgrade your clusters to a newer supported version before November 24, 2025. This change ensures that your Kubernetes environment remains up to date with the latest security patches and feature enhancements, delivering improved performance and reliability.

***

## May 9, 2025 <a href="#may_9_2025" id="may_9_2025"></a>

VKS (GreenNode Kubernetes Service) has just released its latest update, bringing several new features to users. Below are the key highlights of this release:

**New Features:**

* **Automatically delete clusters with no active nodes after 30 days:** The system will automatically scan and delete clusters that have no active nodes for 30 consecutive days. During the scanning period and before deletion, warning emails will be sent to you to ensure you can take action if you need to keep the cluster.
* **Placement Group support for each Node Group:** This feature allows you to more effectively control the physical placement of nodes to optimize performance and ensure high availability—especially useful for workloads that require physical distribution or co-location to reduce latency.
* **Release of Kubernetes version v1.30.10:** The new version v1.30.10 is now available in both HCM and HAN regions with two supported Images (Ubuntu with containerd and Container-Optimized OS with containerd). This version is published under the Rapid release channel—ideal for testing environments or projects that need early access to updates.

***

## April 25, 2025 <a href="#april_25_2025" id="april_25_2025"></a>

VKS (GreenNode Kubernetes Service) has just released its latest update, bringing several improvements to users. Below are the key highlights of this release:

**Improvements:**

* **End of support for Kubernetes version v1.27.12-vks.1724605200 on May 12, 2024:** Starting from April 25, 2025, you will no longer be able to create new clusters with this version. If you are still using this version, please manually upgrade to a newer version before May 12, 2024. After this date, the system will automatically upgrade clusters/node groups running this version to a higher one to ensure stability and security.

If you need assistance, please contact our technical support team through the available support channels.

***

## April 16, 2025 <a href="#april_16_2025" id="april_16_2025"></a>

VKS (GreenNode Kubernetes Service) has just released its latest update, bringing several new features to users. Below are the key highlights of this release:

**New Features:**

* **Private Cluster support in the HAN region:** Previously, Private Cluster—a Kubernetes cluster whose control plane and nodes are fully private and only accessible via internal network—was only available in the HCM region. With this update, you can now:
  * Create and use Private Clusters in the HAN region.
  * Ensure feature consistency between the HCM and HAN regions.
  * Flexibly select the deployment location closest to your workload.

***

## Mar 5, 2025 <a href="#april_19_2024-3" id="april_19_2024-3"></a>

VKS (GreenNode Kubernetes Service) has just released the latest update, bringing many new features to users. Here are the highlights of the update:

**New features:**

* **Support Container-Optimized OS with containerd for HCM region** – Previously, VKS only supported **Ubuntu with containerd** . Now, customers can use **Container-Optimized OS with containerd** as the node group image for node groups when deploying Kubernetes version **v1.29.1, v1.30.5** in HCM region.

***

## Feb 27, 2025 <a href="#april_19_2024-3-1" id="april_19_2024-3-1"></a>

VKS (GreenNode Kubernetes Service) has just released the latest update, bringing many new features to users. Here are the highlights of the update:

**New features:**

* **Fleet management** : allows grouping Kubernetes clusters across multiple **zones/regions** , helping to flexibly manage traffic between clusters. This feature supports:
  * **North-South Traffic Management with Global Load Balancer (GLB)** : Manage inbound/outbound traffic between clusters. Supports **Calico Overlay, Cilium Overlay, and Cilium VPC Native** .
  * **East-West Traffic Management with Multi Cluster Service (MCS)** : Manage internal traffic between clusters, only supports **Cilium VPC Native** .

For details, please refer to the user manual [here ](../network/fleet-management.md).

***

## Jan 20, 2025 <a href="#april_19_2024-3-2" id="april_19_2024-3-2"></a>

VKS (GreenNode Kubernetes Service) has just released the latest update, bringing many new features to users. Here are the highlights of the update:

**New features:**

* **Support Container-Optimized OS with containerd for BKK zone** – Previously, VKS only supported **Ubuntu with containerd** . Now, customers can use **Container-Optimized OS with containerd** as the node group image for node groups when deploying Kubernetes version **v1.29.1, v1.30.5** in BKK zone.

***

## Jan 2, 2025 <a href="#april_19_2024-3-3" id="april_19_2024-3-3"></a>

VKS (GreenNode Kubernetes Service) has just released the latest update, bringing many new features to users. Here are the highlights of the update:

**New features:**

* **Kubernetes v1.30 Support:** This version is in **Release Channel: Rapid** , for testing purposes. VKS does not commit to an SLA for this version.
* **Container-Optimized OS with containerd support for HAN region** – Previously, VKS only supported **Ubuntu with containerd** . Now, customers can use **Container-Optimized OS with containerd** as the node group image for node groups when deploying Kubernetes v1.29.1 **, v1.30.5** in HAN region.
* **Upgrade Insight** – Provides detailed information about Kubernetes version upgrades, helping users assess risk, impact, and plan upgrades effectively.

***

## Dec 5, 2024 <a href="#april_19_2024-3" id="april_19_2024-3"></a>

VKS (GreenNode Kubernetes Service) has just released the latest update, bringing many new features to users. Here are the highlights of the update:

**New features:**

* **Force-Upgrade, Auto-Upgrade** : Automatically upgrade the Kubernetes version for the cluster/node group on schedule or when the current version is about to expire. For more details, please refer [here](https://docs-vngcloud-vn.translate.goog/vng-cloud-document/vn/vks/upgrade-kubernetes-version/automatically-upgrade) .

***

## Oct 23, 2024 <a href="#april_19_2024-3-1" id="april_19_2024-3-1"></a>

VKS (GreenNode Kubernetes Service) has just released the latest update, bringing many improvements to users. Here are the highlights of the update:

**Improve:**

* **Support POC/ Stop POC for Cluster** : Users can now perform POC/ Stop POC for resources on VKS such as Server, Volume, Load Balancer, Endpoint. This feature brings high flexibility to users who want to experience VKS. For more details, please refer [here](https://docs-vngcloud-vn.translate.goog/vng-cloud-document/vn/vks/clusters/stop-poc) .
* **Upgrade GreenNode BlockStorage CSI Driver Plugin:** Bugs discovered in previous versions have been fixed, making the system run smoother and more reliably.
* **Freely choose/edit configuration with/without using GreenNode LoadBalancer Controller plugin, GreenNode LoadBalancer Controller plugin on existing VKS cluster:** The ability to customize plugin configuration allows users to optimize the VKS cluster according to their specific needs. This helps increase flexibility and meet the special requirements of each application.
* **Additionally,** in this update, we have also fixed some minor bugs to provide a better user experience.

***

## Oct 03, 2024 <a href="#april_19_2024-3" id="april_19_2024-3"></a>

VKS (GreenNode Kubernetes Service) has just released its latest update, bringing many features and improvements to users. Here are the highlights of the update:

**New Region:**

* In addition to Region HCM03, VKS now supports Region HAN01. This addition gives customers more options in deploying applications, especially useful for businesses with data location requirements.

**New features:**

* **Network Type: Cilium Overlay, Cilium VPC Native Routing:** In addition to Calico Overlay, this release we have added two new network types: Cilium Overlay and Cilium VPC Native Routing. Cilium Overlay allows you to build flexible overlay networks, while Cilium VPC Native Routing integrates tightly with GreenNode's VPC, optimizing performance and security for your applications. For more details, please refer [here](https://docs-vngcloud-vn.translate.goog/vng-cloud-document/vn/vks/network/cni) .

**Improve:**

* **Multiple Subnets for Clusters on VKS:** VKS now supports using multiple subnets for a cluster. This allows you to configure each node group in the cluster to be located on different subnets within the same VPC, optimizing resource allocation and network management.
* **Edit Labels/Taints on an existing VKS cluster:** With the ability to directly edit Labels/Taints on a deployed VKS cluster, you can control Pod scheduling, apply different policies to Node Groups, and customize node selection rules for applications. This helps manage and classify resources more efficiently.
* **Enable/Disable Private Service Endpoint usage option:** Previously, when creating a private cluster on VKS, creating a private service endpoint was required. Now, you can easily enable/disable this feature, allowing services in the VKS cluster to communicate via internal IP addresses, enhancing security and minimizing the risk of external attacks.
* **Enable/Disable Volume Encryption Option:** Volume encryption feature allows you to protect sensitive data stored in the Persistent Volumes of the VKS cluster. This ensures data security and compliance with information protection regulations. Now, you can enable/disable encryption for each Volume as needed.

***

## Aug 28, 2024 <a href="#april_19_2024-3" id="april_19_2024-3"></a>

VKS (GreenNode Kubernetes Service) introduces the latest update to the existing VKS, bringing many new features to users. Here are the details about the update:

**New features:**

* **Private Cluster**: Previously, public clusters on VKS were using Public IP addresses to communicate between nodes and the control plane. To improve the security of your cluster, we have launched the private cluster model. The Private Cluster feature helps your K8S cluster to be as secure as possible, all connections are completely private from the connection between the nodes to the control plane, the connection from the client to the control plane, or the connection from the nodes to other products and services in GreenNode such as: vStorage, vCR, vMonitor, GreenNode APIs,... **Private Cluster is the ideal choice for services that require strict access control, ensuring compliance with regulations on security and data privacy**. For details on the two operating models of Cluster, you can refer to [here ](../mo-hinh-hoat-dong.md)and refer to the steps to create a private Cluster [here](https://docs-vngcloud-vn.translate.goog/vng-cloud-document/v/vn/vks/getting-started/khoi-tao-mot-private-cluster) .

***

## Aug 26, 2024 <a href="#april_19_2024-3" id="april_19_2024-3"></a>

VKS (GreenNode Kubernetes Service) introduces the latest update for VKS, bringing numerous new improvements for users. Here are the details of the update:

**Improve:**

* **Kubernetes Version**: VKS has added new images to optimize the size, features, and network compared to the old images. The creation of these images also aims to serve both Public and Private clusters that VKS is about to launch. Specifically, in this release, we have added the following images:
  * Ubuntu-22.kube\_v1.27.12-vks.1724605200
  * Ubuntu-22.kube\_v1.28.8-vks.1724605200
  * Ubuntu-22.kube\_v1.29.1-vks.1724605200

{% hint style="info" %}
**Chú ý:**

* Để khởi tạo một **Private Cluster**, bạn cần chọn sử dụng một trong 3 image mới này. Đối với Public Cluster, bạn có thể chọn sử dụng bất kỳ image cũ hoặc mới tùy theo nhu cầu của bạn.
{% endhint %}

***

## Aug 13, 2024 <a href="#april_19_2024-3" id="april_19_2024-3"></a>

VKS (GreenNode Kubernetes Service) introduces the latest update to the existing VKS, bringing many new improvements to users. Here are the details about the update:

**Improve:**

* **Event History**: VKS has added events for **Auto Scaling** and **Auto Healing.** Now, with Event History, you can track every change occurring within your Cluster, from automatic scaling to automatic healing. These events enhance your ability to monitor and manage your Kubernetes cluster.

***

## Aug 01, 2024 <a href="#april_19_2024-3" id="april_19_2024-3"></a>

VKS (GreenNode Kubernetes Service) introduces the latest update to the already available VKS, bringing many new features and improvements to users. Here are the details about the update:

**New feature:**

* **VKS resource monitoring:** Users can directly monitor the operating status of Cluster, Node, CPU usage, RAM, Memory,... status of Node through intuitive dashboards. To display data on the dashboard, users need to install it `vmonitor-metric-agent`on the cluster where they want to perform monitoring. For more details, refer [here](https://docs-vngcloud-vn.translate.goog/vng-cloud-document/v/vn/vks/monitoring/metrics) .

***

## July 25, 2024 <a href="#april_19_2024-3-1" id="april_19_2024-3-1"></a>

VKS (GreenNode Kubernetes Service) introduces the latest update to the existing VKS, bringing many new improvements to users. Here are the details about the update:

**Improve:**

* **Upgrade VKS management through Terraform:** Users can **simultaneously** adjust the number of nodes and change the number of nodes for autoscale (Minimum/ Maximum node Autoscale) right during the configuration editing process. With the ability to adjust multiple parameters at the same time, managing a Kubernetes cluster becomes more flexible and convenient. For more details, see examples [here](https://registry.terraform.io/providers/vngcloud/vngcloud/latest/docs/resources/vks_cluster) .

***

## July 23, 2024 <a href="#april_19_2024-3-2" id="april_19_2024-3-2"></a>

VKS (GreenNode Kubernetes Service) introduces the latest update to the existing VKS, bringing many new improvements to users. Here are the details about the update:

**Improve:**

* **Upgrade GreenNode LoadBalancer Controller Plugin, GreenNode LoadBalancer Controller Plugin:** Errors discovered in previous versions have been fixed, helping the system operate smoother and more reliably.

***

## July 18, 2024 <a href="#april_19_2024" id="april_19_2024"></a>

VKS (GreenNode Kubernetes Service) introduces the latest update to the existing VKS, bringing many new improvements to users. Here are the details about the update:

**Improve:**

* **Upgrade GreenNode BlockStorage CSI Driver Plugin:** Errors discovered in previous versions have been fixed, helping the system operate smoother and more reliably.

***

## July 17, 2024 <a href="#april_19_2024-1" id="april_19_2024-1"></a>

VKS (GreenNode Kubernetes Service) introduces the latest update to the already available VKS, bringing many improvements to users. Here are the details about the update:

**Improve:**

* **Private Node Group** : The MTU for Nodes belonging to the Private Node Group has been updated to 1450. This improves network performance for applications running in the Private Node Group.
* **Number of nodes and AutoScale** : You can now edit both of these properties in the same API. This simplifies your Cluster management.

***

## July 02, 2024 <a href="#april_19_2024-2" id="april_19_2024-2"></a>

VKS (GreenNode Kubernetes Service) introduces the latest update to the already available VKS, bringing many new features and improvements to users. Here are the details about the update:

**New feature:**

* **Stop POC support for Cluster** : Users can now easily perform Stop POC for all resources being POC on a Cluster, instead of having to perform Stop POC individually for each resource. This helps save time and effort when moving Cluster from test resources to real resources. For more details, refer [here](https://docs-vngcloud-vn.translate.goog/vng-cloud-document/v/vn/vks/clusters/stop-poc) .

**Improve:**

* **Node Group status : Added " Degraded** " status so users can monitor the operating status of the Node Group more accurately. This status will display when the number of active nodes is less than the actual number of replicas.
* **Timeout for Cluster and Node Group** : Added timeout for Cluster and Node Group creation, this improvement ensures VKS operates smoothly and efficiently, while providing clear and timely information to users. use. Timeout for Cluster creation is **1 hour** and for Node Group is **3 hours** . If after this time your Cluster or Node Group has not been successfully created, we will update their status to ERROR. At this point, you can delete and create another Cluster or Node Group instead.
* **KubeConfig Access:** Access to the KubeConfig file is now only allowed when the Cluster is Active. This improvement helps users avoid configuration errors when using Terraform to automate Kubernetes deployments.

***

## June 27, 2024 <a href="#april_19_2024-4" id="april_19_2024-4"></a>

VKS (GreenNode Kubernetes Service) introduces the latest update to the existing VKS, bringing many new improvements to users. Here are the details about the update:

**Improve:**

* **Upgrade GreenNode LoadBalancer Controller Plugin, GreenNode LoadBalancer Controller Plugin:** Errors discovered in previous versions have been fixed, helping the system operate smoother and more reliably.

***

## June 19, 2024 <a href="#april_19_2024-5" id="april_19_2024-5"></a>

VKS (GreenNode Kubernetes Service) introduces the latest update to the existing VKS, bringing many new improvements to users. Here are the details about the update:

**Improve:**

* **Upgrade PVC size setting feature (Persistent Volume Claim Size):** Users can now specify the minimum size for CSI drives to be **1GB** instead of the minimum size of 20GB as before. For more details, you can refer to Volume and Integrate with Container Storage Interface .
* **Change the default Storage Class used for Cluster:** change the default from SSD type drives - IOPS 200 to the default SSD type drives - IOPS 3000.
* **Upgrade GreenNode LoadBalancer Controller Plugin, GreenNode LoadBalancer Controller Plugin:** plugin improvements help avoid duplicate Load Balancer naming.

**Attention:**

Because the old default Storage Class has been removed from the system, if you want to continue using and resize this storage class, you can:

* Create a Storage Class named sc-iops-200-retain with the Volume Type you desire.
* Resize Storage Class via command:

Copy

```
kubectl patch pvc sc-iops-200-retain -p '{"spec":{"resources":{"requests":{"storage":"50Gi"\}}\}}'
```

For more details, refer to Integrate with Container Storage Interface .

***

## June 12, 2024 <a href="#april_19_2024-6" id="april_19_2024-6"></a>

VKS (GreenNode Kubernetes Service) introduces the latest update to the already available VKS, bringing many new features and improvements to users. Here are the details about the update:

**New feature:**

* **Support users working with VKS through Terraform:** Users can easily create Clusters and Node Groups in VKS using Terraform. For more details, refer here .

**Improve:**

* **Upgrade GreenNode LoadBalancer Controller Plugin:** Add Annotation to configure Load Balancer to support Proxy Protocol. For more details, refer [here](../network/working-with-nlb/preserve-source-ip-when-using-nlb-and-nginx-ingress-controller.md) .

***

## May 30, 2024 <a href="#april_19_2024-7" id="april_19_2024-7"></a>

We are extremely pleased to announce that the official release ( **General Availability** ) of GreenNode Kubernetes Service is available. With this official release, in addition to the features we have provided on previous releases, this version will bring many new features and improvements to users. Here are the details about the update:

**New feature:**

* **Re-activate:** VKS allows you to request the system to automatically re-initialize the default IAM Service Account when you mistakenly delete or change previously created default IAM Service Account information. The default IAM Service Account is the IAM Service Account that is automatically created by the VKS system when you start working with VKS. We will use this IAM Service Account to initialize resources for your Cluster.
* **Event History** : VKS will display the history of events that occur when users work with the Cluster or each Node Group. This will be a way to help you monitor activities occurring with your Cluster, thereby limiting unusual activities from occurring.
* **Volume** : VKS has integrated the display of the Volume list at the Resource Tab, helping you easily manage the Volumes that are attached to your Cluster.
* **Load Balancer** : VKS has integrated the display of the Load Balancer list in the Resource Tab, helping you easily manage the Load Balancers being used for your Cluster.

**Improve:**

* **Performance** : VKS has optimized performance when initializing the Cluster. Specifically, in the Alpha version, the time from the start of Cluster initialization (with Default Node Group) to the time the Cluster switches to **ACTIVE** state is about 04:00s to 04:30s. Currently, this time has been optimized by us to **02:30s to 03:00s** depending on each Cluster and each time you initialize.
* **Garbage collection of unused containers and images** : VKS will automatically delete unused images when the disk reaches the usage limit (usage/quota ratio >= 85%).
* **In addition** , this GA version has improved a few other issues such as:
  * Changing **the Node Name** helps you easily use and manage your Cluster. Specifically, the Node Name will have additional information: **Cluster Name** , **Node Group Name** .
  * Delete **User Builder** on User's Worker Node.
  * **Change SSH** mechanism from Port 22 to Port 234.

If you encounter any problems with this official release, please contact VKS support for assistance.

***

## May 03, 2024 <a href="#april_19_2024-8" id="april_19_2024-8"></a>

The latest update for VKS is available, bringing many new features and improvements to users. Here are the details about the update:

**New feature:**

* **Supports Whitelist feature:** VKS allows creating a Private Node Group with only Private IP and also allows any IP to connect to the Cluster through the Whitelist IP feature. For more details, please refer to Whitelist .

**Improve:**

* **System optimization:** Helps the system operate more smoothly and efficiently.
* **Bug Fixes:** Fixed some minor bugs to provide a better user experience.

If you encounter any problems after updating, please contact VKS support for assistance.

***

## April 17, 2024 <a href="#april_19_2024-9" id="april_19_2024-9"></a>

**We're excited to introduce a new update to the VKS service, giving you a more powerful and efficient Kubernetes management experience than ever before!**

**Highlights:**

* **Fully Managed control plane:** VKS will free you from the burden of managing Kubernetes' Control Plane, helping you focus on application development.
* **Supports the latest versions of Kubernetes:** VKS always updates the latest versions of Kubernetes (minor versions from 1.27, 1.28, 1.29) to ensure you always take advantage of the most advanced features.
* **Kubernetes Networking:** VKS integrates Calico CNI, providing high efficiency and security.
* **Upgrade seamlessly:** VKS supports upgrading between Kubernetes versions easily and quickly, helping you stay up to date with the latest improvements.
* **Scaling & Healing Automatically:** VKS automatically expands the Node group when necessary and automatically fixes errors when the node has problems, helping you save time and effort on management.
* **Reduce costs and improve reliability:** VKS deploys Control Plane of Kubernetes in high availability mode and is completely free, helping you save costs and improve system reliability.
* **Blockstore Native (Container Storage Interface - CSI) integration:** VKS allows you to manage Blockstore through Kubernetes' YAML, providing persistent storage for containers and supporting important features such as resizing, IOPS scaling and snapshot volumes.
* **Integrate Load Balancer (Network Load Balancer, Application Load Balancer) through built-in drivers such as GreenNode Controller Mananger, GreenNode LoadBalancer Controller:** VKS provides the ability to manage NLB/ALB through YAML of Kubernetes, making it easy for you expose Service in Kubernetes to the outside.
* **Enhance security:** VKS allows you to create a Private Node Group with only Private IP and control access to the cluster through the IP Whitelist feature, ensuring the safety of your system.

**With these breakthrough features, VKS promises to bring you a completely new Kubernetes management experience, helping you optimize efficiency and save costs!**

**Please contact us for further advice and support!**
