---
title: What are providers in Azure Monitor for SAP solutions? (preview)
description: This article provides answers to frequently asked questions about Azure monitor for SAP solutions providers.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: conceptual
ms.date: 07/28/2022
ms.author: radeltch
#Customer intent: As a developer, I want to learn what providers are available for Azure Monitor for SAP solutions so that I can connect to these providers.
---

# What are providers in Azure Monitor for SAP solutions? (preview)

[!INCLUDE [Azure Monitor for SAP solutions public preview notice](./includes/preview-azure-monitor.md)]

In the context of *Azure Monitor for SAP solutions (AMS)*, a *provider* contains the connection information for a corresponding component and helps to collect data from there. There are multiple provider types. For example, an SAP HANA provider is configured for a specific component within the SAP landscape, like an SAP HANA database. You can configure an AMS resource (also known as SAP monitor resource) with multiple providers of the same type or multiple providers of multiple types.

This content applies to both versions of the service, *AMS* and *AMS (classic)*.
   
You can choose to configure different provider types for data collection from the corresponding component in their SAP landscape. For example, you can configure one provider for the SAP HANA provider type, another provider for high availability cluster provider type, and so on.  

You can also configure multiple providers of a specific provider type to reuse the same SAP monitor resource and associated managed group. For more information, see [Manage Azure Resource Manager resource groups by using the Azure portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md).

![Diagram showing AMS connection to available providers.](./media/azure-monitor-providers/providers.png)

It's recommended to configure at least one provider when you deploy an AMS resource. By configuring a provider, you start data collection from the corresponding component for which the provider is configured.   

If you don't configure any providers at the time of deployment, the AMS resource is still deployed, but no data is collected. You can add providers after deployment through the SAP monitor resource within the Azure portal. You can add or delete providers from the SAP monitor resource at any time.

## Provider type: SAP NetWeaver

You can configure one or more providers of provider type SAP NetWeaver to enable data collection from SAP NetWeaver layer. AMS NetWeaver provider uses the existing [**SAPControl** Web service](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) interface to retrieve the appropriate information.

For the current release, the following SOAP web methods are the standard, out-of-box methods invoked by AMS.

| Web method | ABAP support | Java support | Metrics |
| ---------- | ------------ | ------------ | ------- |
| **GetSystemInstanceList** | Yes | Yes | Instance availability, message server, gateway, ICM, ABAP availability |
| **GetProcessList** | Yes | Yes | If instance list is red, you can find what process caused the issue |
| **GetQueueStatistic** | Yes | Yes | Queue statistics (DIA, BATCH, UPD) |
| **ABAPGetWPTable** | Yes | No | Work process utilization |
| **EnqGetStatistic** | Yes | Yes | Locks |

You can get the following data with the SAP NetWeaver provider: 

- SAP system and application server availability
    - Instance process availability of dispatcher
    - ICM
    - Gateway
    - Message server
    - Enqueue Server
    - IGS Watchdog
- Work process usage statistics and trends
- Enqueue Lock statistics and trends
- Queue usage statistics and trends
- SMON Metrics (**/SDF/SMON**)
- SWNC Workload, Memory, Transaction, User, RFC Usage (St03n)
- Short Dumps (**ST22**)
- Object Lock (**SM12**)
- Failed Updates (**SM13**)
- System Logs Analysis (**SM21**)
- Batch Jobs Statistics (**SM37**)
- Outbound Queues (**SMQ1**)
- Inbound Queues (**SMQ2**)
- Transactional RFC (**SM59**)
- STMS Change Transport System Metrics (**STMS**)

![Diagram showing the NetWeaver provider architecture.](./media/azure-monitor-providers/netweaver-architecture.png)

## Provider type: SAP HANA

You can configure one or more providers of provider type *SAP HANA* to enable data collection from SAP HANA database. The SAP HANA provider connects to the SAP HANA database over SQL port, pulls data from the database, and pushes it to the Log Analytics workspace in your subscription. The SAP HANA provider collects data every 1 minute from the SAP HANA database.  

You can see the following data with the SAP HANA provider:

- Underlying infrastructure usage
- SAP HANA host status
- SAP HANA system replication
- SAP HANA Backup data

Configuring the SAP HANA provider requires:
- The host IP address,
- HANA SQL port number
- **SYSTEMDB** username and password

It's recommended to configure the SAP HANA provider against **SYSTEMDB**. However, more providers can be configured against other database tenants.

![Diagram shows Azure Monitor for SAP solutions providers - SAP HANA architecture.](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## Provider type: Microsoft SQL server

You can configure one or more Microsoft SQL Server providers to enable data collection from [SQL Server on Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). The SQL Server provider connects to Microsoft SQL Server over the SQL port. It then pulls data from the database and pushes it to the Log Analytics workspace in your subscription. Configure SQL Server for SQL authentication and for signing in with the SQL Server username and password. Set the SAP database as the default database for the provider. The SQL Server provider collects data from every 60 seconds up to every hour from the SQL server.  

In public preview, you can expect to see the following data with SQL Server provider:
- Underlying infrastructure usage
- Top SQL statements
- Top largest table
- Problems recorded in the SQL Server error log
- Blocking processes and others  

Configuring Microsoft SQL Server provider requires:
- The SAP System ID
- The Host IP address
- The SQL Server port number
- The SQL Server username and password

![Diagram shows Azure Monitor for SAP solutions providers - SQL architecture.](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## Provider type: High-availability cluster

You can configure one or more providers of provider type *High-availability cluster* to enable data collection from Pacemaker cluster within the SAP landscape. The High-availability cluster provider connects to Pacemaker using the [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) for **SUSE** based clusters and by using [Performance co-pilot](https://access.redhat.com/articles/6139852) for **RHEL** based clusters. AMS then pulls data from the database and pushes it to Log Analytics workspace in your subscription. The High-availability cluster provider collects data every 60 seconds from Pacemaker.  

In public preview, you can expect to see the following data with the High-availability cluster provider:   
 - Cluster status represented as a roll-up of node and resource status 
 - Location constraints
 - Trends
 - [others](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Diagram shows Azure Monitor for SAP solutions providers - High-availability cluster architecture.](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

To configure a High-availability cluster provider, two primary steps are involved:

1. Install [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) in *each* node within the Pacemaker cluster.

   You have two options for installing ha_cluster_exporter:
   
   - Use Azure Automation scripts to deploy a high-availability cluster. The scripts install [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) on each cluster node.  
   - Do a [manual installation](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build). 

2. Configure a High-availability cluster provider for *each* node within the Pacemaker cluster.

   To configure the High-availability cluster provider, the following information is required:
   
   - **Name**. A name for this provider. It should be unique for this Azure Monitor for SAP solutions instance.
   - **Prometheus Endpoint**. `http://<servername or ip address>:9664/metrics`.
   - **SID**. For SAP systems, use the SAP SID. For other systems (for example, NFS clusters), use a three-character name for the cluster. The SID must be distinct from other clusters that are monitored.   
   - **Cluster name**. The cluster name used when creating the cluster. The cluster name can be found in the cluster property `cluster-name`.
   - **Hostname**. The Linux hostname of the virtual machine (VM).  

## Provider type: OS (Linux)

You can configure one or more providers of provider type OS (Linux) to enable data collection from a BareMetal or VM node. The OS (Linux) provider connects to BareMetal or VM nodes using the [Node_Exporter](https://github.com/prometheus/node_exporter) endpoint. It then pulls data from the nodes and pushes it to Log Analytics workspace in your subscription. The OS (Linux) provider collects data every 60 seconds for most of the metrics from the nodes. 

In public preview, you can expect to see the following data with the OS (Linux) provider: 
   - CPU usage, CPU usage by process 
   - Disk usage, I/O read & write 
   - Memory distribution, memory usage, swap memory usage 
   - Network usage, network inbound & outbound traffic details 

To configure an OS (Linux) provider, two primary steps are involved:

1. Install [Node_Exporter](https://github.com/prometheus/node_exporter) on each BareMetal or VM node.
   You have two options for installing [Node_exporter](https://github.com/prometheus/node_exporter): 
      - For automated installation with Ansible, use [Node_Exporter](https://github.com/prometheus/node_exporter) on each BareMetal or VM node to install the OS (Linux) Provider.  
      - Do a [manual installation](https://prometheus.io/docs/guides/node-exporter/).

1. Configure an OS (Linux) provider for each BareMetal or VM node instance in your environment. 
   To configure the OS (Linux) provider, the following information is required: 
      - **Name**: a name for this provider, unique to the AMS instance. 
      - **Node Exporter endpoint**: usually `http://<servername or ip address>:9100/metrics`. 

Port 9100 is exposed for the **Node_Exporter** endpoint.

> [!Warning]
> Make sure **Node-Exporter** keeps running after the node reboot. 

## Provider type: IBM Db2

You can configure one or more IBM Db2 providers. The following data is available with this provider type:

- Database availability
- Number of connections
- Logical and physical reads
- Waits and current locks
- Top 20 runtime and executions 

![Diagram shows Azure Monitor for SAP solutions providers - IBM Db2 architecture.](./media/azure-monitor-sap/azure-monitor-providers-db2.png)

## Next steps

Learn how to deploy Azure Monitor for SAP solutions from the Azure portal.

> [!div class="nextstepaction"]
> [Deploy Azure Monitor for SAP solutions by using the Azure portal](./azure-monitor-sap-quickstart.md)
