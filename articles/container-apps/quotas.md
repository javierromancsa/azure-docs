---
title: Quotas for Azure Container Apps
description: Learn about quotas for Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.custom: event-tier1-build-2022
ms.topic: conceptual
ms.date: 08/10/2022
ms.author: cshoe
---

# Quotas for Azure Container Apps

The following quotas are on a per subscription basis for Azure Container Apps.

| Feature | Quantity | Scope | Remarks |
|--|--|--|--|
| Environments | 5 | For a subscription per region | |
| Container Apps | 20 | Environment | |
| Revisions | 100 | Container app | |
| Replicas | 30 | Revision | |
| Cores | 2 | Replica | Maximum number of cores that can be requested by a revision replica. |
| Cores | 20 | Environment | Calculated by the total cores an environment can accommodate. For instance, the sum of cores requested by each active replica of all revisions in an environment. |

## Considerations

* Pay-as-you-go and trial subscriptions are limited to 1 environment per region per subscription.
* If an environment runs out of allowed cores:
  * Provisioning times out with a failure
  * The app silently refuses to scale out

To request an increase in quota amounts for your container app, [submit a support ticket](https://azure.microsoft.com/support/create-ticket/).
