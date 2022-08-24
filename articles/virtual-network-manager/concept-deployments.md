---
title: 'Configuration deployments in Azure Virtual Network Manager (Preview)'
description: Learn about how configuration deployments work in Azure Virtual Network Manager.
author: mbender-ms    
ms.author: mbender
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 07/06/2022
ms.custom: template-concept, ignite-fall-2021
---

# Configuration deployments in Azure Virtual Network Manager (Preview)

In this article, you'll learn about how configurations are applied to your network resources. You'll also explore how updating a configuration deployment is different for each membership type. Then we'll go into details about *Deployment status* and *Goal state model*.

> [!IMPORTANT]
> Azure Virtual Network Manager is currently in public preview.
> This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Deployment

*Deployment* is the method Azure Virtual Network Manager uses to apply configurations to your virtual networks in network groups. Configurations won't take effect until they're deployed. Changes to network groups, including events such as removal and addition of a virtual network into a network group, will take effect without the need for redeployment. For example, if you have a configuration deployed, and a virtual network is added to a network group, it takes effect immediately. When committing a deployment, you select the region(s) to which the configuration will be applied. When a deployment request is sent to Azure Virtual Network Manager, it will calculate the [goal state](#goalstate) of network resources and request the necessary changes to your infrastructure. The changes can take a few minutes depending on how large the configuration is.

*Deployment* is the method Azure Virtual Network Manager uses to apply configurations to your virtual networks in network groups. Configurations won't take effect until they're deployed. When a deployment request is sent to Azure Virtual Network Manager, it will calculate the [goal state](#goalstate) of all resources under your network manager in that region. Goal state is a combination of deployed configurations and network group membership. Network manager will then apply the necessary changes to your infrastructure.

When committing a deployment, you select the region(s) to which the configuration will be applied. The deployed configuration is also static. Once deployed, you can edit your configurations freely without impacting your deployed setup. Applying any of these new changes will take another deployment. The changes reprocess the entire region and can take a few minutes depending on how large the configuration is. However, Changes to network groups will take effect without the need for redeployment. This includes adding or removing group members directly, or configuring an Azure Policy resource. Safe deployment practices recommend gradually rolling out changes on a per-region basis. 

## Deployment status

When you commit a configuration deployment, the API does a POST operation. Once the deployment request has been made, Azure Virtual Network Manager will calculate the goal state of your networks in the deployed regions and request the underlying infrastructure to make the changes. You can see the deployment status on the *Deployment* page of the Virtual Network Manager.

:::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployment-in-progress.png" alt-text="Screenshot of deployment in progress in deployment list.":::

## <a name = "goalstate"></a> Goal state model

When you commit a deployment of configuration(s), you're describing the goal state of your network manager in that region. This goal state is enforced during the next deployment. For example, when you commit configurations named *Config1* and *Config2* into a region, these two configurations get applied and become the region's goal state. If you decided to commit configuration named *Config1* and *Config3* into the same region, *Config2* would then be removed, and *Config3* would be added. To remove all configurations, you would deploy a **None** configuration against the region(s) you no longer wish to have any configurations applied.

## Next steps

Learn how to [create an Azure Virtual Network Manager instance](create-virtual-network-manager-portal.md).
