---
title: Migrate integration resources to the target Azure region
description: This article provides the guidance and tools on migrating integration resources to targey Azure region.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/16/2019
ms.author: frankch

---



# Migrate integration resources to the target Azure region

This section contains information that you may find helpful as you migrate your Azure integration resources across Azure regions.

## Service Bus

Azure Service Bus services don’t have data export or import capabilities. To migrate Service Bus resources across Azure regions, you can export the resources [as an Azure Resource Manager template](https://docs.azure.cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates). Then, adapt the exported template for the target Azure region and re-create the resources.

>*NOTE*  
>*Exporting a Resource Manager template doesn’t copy the data (for example, messages). Exporting a template only re-creates the metadata.*

>*IMPORTANT*  
>*Change location, Azure Key Vault secrets, certificates, and other GUIDs to be consistent with the new region.*

### Service Bus metadata
The following Service Bus metadata elements are re-created when you export a Resource Manager template:
*	Namespaces
* Queues
* Topics
* Subscriptions
* Rules
* Authorization rules

### Keys

The preceding steps to export and re-create don’t copy the shared access signature keys that are associated with authorization rules. If you need to preserve the shared access signature keys, use the New-AzureRmServiceBuskey cmdlet with the optional parameter
-Keyvalue to accept the key as a string. The updated cmdlet is available in the [PowerShell Gallery release 6.4.0 (July 2018)](https://www.powershellgallery.com/packages/AzureRM/6.4.0) or on [GitHub](https://github.com/Azure/azure-powershell/releases/tag/v6.4.0-July2018).

 

### Usage example
```PowerShell
New-AzureRmServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string-keyvalue>
```
```powershell
New-AzureRmServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Queue <queuename> -Name <name of Authorization rule> - RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string-keyvalue>
```
```powershell
New-AzureRmServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Topic <topicname> -Name <name of Authorization rule> - RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string-keyvalue>
```
>*NOTE*  
>*You must update your applications to use a new connection string even if you preserve the keys.*

For more information: 
* Refresh your knowledge by completing the [Service Bus tutorials](https://docs.azure.cn/service-bus-messaging/#step-by-step-tutorials).
* Become familiar with how to [export Resource Manager templates](https://docs.azure.cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates) or read the overview of [Azure Resource Manager](https://docs.azure.cn/azure-resource-manager/resource-group-overview).



## Logic Apps

Azure Scheduler will [retire on September 30, 2019](https://azure.microsoft.com/updates/azure-scheduler-will-retire-on-september-30-2019/). Use Logic Apps to create scheduling jobs in the target Azure region. For detailed steps on migrating from Scheduler to Logic Apps, see [Migrate Azure Scheduler jobs to Azure Logic Apps](https://docs.azure.cn/scheduler/migrate-from-scheduler-to-logic-apps).


For more information: 
*	Become familiar with features in Azure Logic Apps by completing the [Logic Apps tutorials](https://docs.azure.cn/logic-apps/#step-by-step-tutorials).
* Review the [Azure Logic Apps overview](https://docs.azure.cn/logic-apps/logic-apps-overview).

## API Management

See **API Management** chapter of [Migration Web Resources](./china-migration-guidance-web.md).

## Event Grid
(place holder)
