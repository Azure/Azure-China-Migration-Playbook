---
title: Migrate Analytics Resources
description: This article provides the guidance and tools on migrating analytics resources.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/16/2019
ms.author: frankch

---


# Migrate Analytics Resources

This section provides information that you may find helpful as you migrate Azure analytics resources across Azure regions.

## HDInsight

To migrate HDInsight services across regions, you can export HDInsight resources [as a Resource Manager template](https://docs.azure.cn/en-us/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates) and adapt the exported template for the target Azure region and re-create the resources.

>*NOTE*  
>*Exporting an HDInsight template doesn’t copy data (for example, temp data). Exporting a template only re-creates HDInsight metadata.*

To migrate Azure HDInsight clusters across Azure regions:
1. Stop the HDInsight cluster.
2.	Migrate the data in the Azure Storage account to the new region by using AzCopy or a similar tool.
3. Create new HDInsight resource in the target Azure region, and then attach the migrated storage resources as the primary attached storage.

For more specialized, long-running clusters (Kafka, Spark streaming, Storm, or HBase), we recommend that you orchestrate the transition of workloads to the new region.

For more information:
*	Review the [Azure HDInsight documentation](https://docs.azure.cn/en-us/hdinsight/).
*	Refresh your knowledge by completing the [HDInsight tutorials](https://docs.azure.cn/en-us/hdinsight/#step-by-step-tutorials).
*	For help with [scaling HDInsight clusters](https://docs.azure.cn/en-us/hdinsight/hdinsight-administer-use-powershell#scale-clusters), see [Administer HDInsight by using PowerShell](https://docs.azure.cn/en-us/hdinsight/hdinsight-administer-use-powershell).
*	Learn how to use [AzCopy](https://docs.azure.cn/en-us/storage/common/storage-use-azcopy-v10).



## Event Hubs

You can’t directly migrate Azure Event Hubs resources across Azure regions. The Event Hubs service doesn’t have data export or import capabilities. You can export Event Hubs resources [as a Resource Manager template](https://docs.azure.cn/en-us/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates), adapt the exported template for the target Azure region and re-create the resources.



>*NOTE*  
>*Exporting an Event Hubs template doesn’t copy data (for example, messages). Exporting a template only re-creates Event Hubs metadata. *

>*IMPORTANT*  
>*Change location, Azure Key Vault secrets, certificates, and other GUIDs to be consistent with the new region.*

### Event Hubs metadata

The following metadata elements are re-created when you export an Event Hubs template: 
*	Namespaces
*	Event hubs
*	Consumer groups
*	Authorization rules

For more information:
*	Review the [Event Hubs overview](https://docs.azure.cn/en-us/event-hubs/event-hubs-about).
*	Refresh your knowledge by completing the [Event Hubs tutorials](https://docs.azure.cn/en-us/event-hubs/#step-by-step-tutorials).
*	Check the migration steps for **Azure Service Bus** .
*	Become familiar with how to [export Azure Resource Manager templates](https://docs.azure.cn/en-us/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates) or read the overview of [Azure Resource Manager](https://docs.azure.cn/en-us/azure-resource-manager/resource-group-overview).



 
## Stream Analytics

To migrate Azure Stream Analytics services across Azure regions, the easiest way is to copy a job to other regions using Visual Studio Tools for Azure Stream Analytics. More information in this [blog post](https://azure.microsoft.com/en-us/blog/5-tips-to-get-more-out-of-azure-stream-analytics-visual-studio-tools/).

You can also manually re-create the entire setup in the target Azure region either by using the Azure portal or by using PowerShell. Ingress and egress sources for a Stream Analytics job can be in any region.

For more information: 
*	Refresh your knowledge by completing the [Stream Analytics tutorials](https://docs.azure.cn/en-us/stream-analytics/#step-by-step-tutorials).
*	Review the [Stream Analytics overview](https://docs.azure.cn/en-us/stream-analytics/stream-analytics-introduction).
*	Learn how to [create a Stream Analytics job by using PowerShell](https://docs.azure.cn/en-us/stream-analytics/stream-analytics-quick-create-powershell).



## Analysis Services

To migrate your Azure Analysis Services models across Azure regions, use the [backup and restore operations](https://docs.azure.cn/en-us/analysis-services/analysis-services-backup).

If you want to migrate only the model metadata and not the data, an alternative is to [redeploy the model from SQL Server Data Tools](https://docs.azure.cn/analysis-services/analysis-services-deploy).

 
 For more information: 
*	Learn about [Analysis Services backup and restore](https://docs.azure.cn/analysis-services/analysis-services-backup).
*	Review the [Analysis Services overview](https://docs.azure.cn/analysis-services/analysis-services-overview).

## PowerBI

Migrating PowerBI across Azure regions is not supported at this time, but you can migrate selected workspaces to another Azure region by using Power BI Premium. For more information, see [Configure Multi-Geo support for Power BI Premium](https://docs.microsoft.com/en-us/power-bi/service-admin-premium-multi-geo).

## SQL Data Warehouse

Migrating SQL Data Warehouse across Azure regions isn’t supported at this time.

## Data Factory
Review the **Data Factory** chapter of [Migrate Database Resources](./china-migration-guidance-databases.md). 
(blank)


## Azure Data Explorer
(place holder)


