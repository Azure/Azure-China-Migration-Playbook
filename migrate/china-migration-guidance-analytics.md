---
title: 迁移分析服务
description: This article provides the guidance and tools on migrating analytics resources.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/26/2019
ms.author: frankch

---


# 迁移分析服务

本节提供的信息可帮助您跨 Azure 区域迁移 Azure 分析服务。

## HDInsight

要跨区域迁移 HDInsight 服务，可以将 HDInsight 资源导出为[资源管理器模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)，然后针对目标 Azure 区域调整导出的模板并重新创建资源。

>*注意：*  
>*导出 HDInsight 模板不会复制数据（例如，临时数据）。导出模板仅重新创建 HDInsight 元数据。*

要跨 Azure 区域迁移 Azure HDInsight 群集：
1. 停止 HDInsight 群集。
2. 使用 AzCopy 或类似工具将 Azure 存储帐户中的数据迁移到新区域。
3. 在目标 Azure 区域中创建新的 HDInsight 资源，然后将迁移的存储资源作为主要连接存储附加。

对于更加特殊化、长期运行的群集（Kafka、Spark 流、Storm 或 HBase），我们建议您将工作负载转换到新区域。

有关更多信息：
* 查阅 [Azure HDInsight 文档](https://docs.azure.cn/zh-cn/hdinsight/)。
* 通过完成 [HDInsight 教程](https://docs.azure.cn/zh-cn/hdinsight/#step-by-step-tutorials)来刷新您的知识。
* 有关[缩放 HDInsight 群集](https://docs.azure.cn/zh-cn/hdinsight/hdinsight-administer-use-powershell#scale-clusters)的帮助，请参阅[使用 PowerShell 管理 HDInsight](https://docs.azure.cn/zh-cn/hdinsight/hdinsight-administer-use-powershell)。
* 了解如何使用 [AzCopy](https://docs.azure.cn/zh-cn/storage/common/storage-use-azcopy-v10)。

## 事件中心

您无法跨 Azure 区域直接迁移 Azure 事件中心资源。事件中心服务没有数据导出或导入功能。您可以将事件中心资源导出为[资源管理器模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)，然后针对目标 Azure 区域调整导出的模板并重新创建资源。

>*注意:*  
>*导出事件中心模板不会复制数据（例如，消息）。导出模板仅重新创建事件中心元数据。*

>*重要事项：*  
>*更改位置、Azure 密钥保管库密码、证书和其他 GUID 以与新区域保持一致。*

### 事件中心元数据

导出事件中心模板时，将重新创建以下元数据元素：
* 命名空间
* 事件中心
* 使用者组
* 授权规则

有关更多信息：
* 查阅[事件中心概述](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-about)。
* 通过完成[事件中心教程](https://docs.azure.cn/zh-cn/event-hubs/#step-by-step-tutorials)来刷新您的知识。
* 检查**Azure 服务总线**章节的迁移步骤。
* 熟悉如何[导出 Azure 资源管理器模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)，或阅读[ Azure 资源管理器](https://docs.azure.cn/zh-cn/azure-resource-manager/resource-group-overview)的概述。
 
## 流分析

要跨 Azure 区域迁移 Azure 流分析服务，最简单的方法是使用适用于 Azure 流分析的 Visual Studio 工具将作业复制到其他区域。查阅此[博客文章](https://azure.microsoft.com/zh-cn/blog/5-tips-to-get-more-out-of-azure-stream-analytics-visual-studio-tools/)以了解更多信息。  
您还可以使用 Azure 门户或使用 PowerShell 在目标 Azure 区域中手动重新搭建服务。流分析作业的入口和出口源可以位于任何区域。

有关更多信息：
* 通过完成[流分析教程](https://docs.azure.cn/zh-cn/stream-analytics/#step-by-step-tutorials)来刷新您的知识。
* 查阅[流分析概述](https://docs.azure.cn/zh-cn/stream-analytics/stream-analytics-introduction)。
* 了解如何[使用 PowerShell 创建流分析作业](https://docs.azure.cn/zh-cn/stream-analytics/stream-analytics-quick-create-powershell)。

## Analysis Services

要跨 Azure 区域迁移 Azure Analysis Services 模型，请使用[备份和还原操作](https://docs.azure.cn/zh-cn/analysis-services/analysis-services-backup)。

如果只想迁移模型元数据而不是数据，则可以选择[从 Visual Studio 部署模型](https://docs.azure.cn/zh-cn/analysis-services/analysis-services-deploy)。
 
有关更多信息：
* 了解 [Analysis Services 备份和还原](https://docs.azure.cn/zh-cn/analysis-services/analysis-services-backup)。
* 查阅[分析服务概述](https://docs.azure.cn/zh-cn/analysis-services/analysis-services-overview)。

## PowerBI

目前不支持跨 Azure 区域迁移 PowerBI ，但您可以使用 Power BI Premium 将选定的工作区迁移到另一个 Azure 区域。
有关更多信息，请参阅[为 Power BI Premium 配置多地理位置支持](https://docs.microsoft.com/zh-cn/power-bi/service-admin-premium-multi-geo)。

## SQL数据仓库

目前不支持跨 Azure 区域迁移 SQL 数据仓库。

## 数据工厂

请参阅[迁移数据库资源](./china-migration-guidance-databases.md)的**数据工厂**章节。(blank)

## Azure 数据资源管理器
(place holder)


