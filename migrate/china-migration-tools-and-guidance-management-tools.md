---
title: 迁移管理工具资源
description: This article provides the guidance and tools on migrating management tool resources.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/06/2019
ms.author: frankch

---


# 迁移管理工具资源

本文提供的信息可帮助您跨 Azure 区域迁移 Azure 管理工具。

## 备份

Azure 备份是基于 Azure 的服务，用于备份（或保护）和还原 Azure 中的数据。该服务可以保护本机 Azure 资源（如 VM）以及来自混合环境的非 Azure 资源（包括在本地服务器上运行的工作负载）。所有备份都存储在 Azure 的恢复服务保管库中。
如果客户需要在 Azure 区域之间迁移，则应首先在目标区域中创建可以保护新区域中资源的新恢复服务保管库。要迁移受 Azure 备份保护的 Azure 资源，客户首先需要停止对资源的保护并保留现有数据（[说明](https://docs.azure.cn/zh-cn/backup/backup-azure-manage-vms#stop-protecting-a-vm)）。然后，可以将资源迁移到目标区域，并且可以在新创建的恢复保管库中启用保护。
对于希望统一备份的客户，Azure 备份正在尝试提供一种工具，以便将现有数据移动到目标区域，同时确保客户仍可访问现有恢复点。该工具预计将在 CY20H1 中提供。

有关更多信息：
* 通过完成[备份教程](https://docs.azure.cn/zh-cn/backup/#step-by-step-tutorials)来刷新您的知识。
* 查阅[Azure 备份概述](https://docs.azure.cn/zh-cn/backup/backup-overview)。

## 计划程序

Azure 计划程序[将于2019年9月30日停用](https://azure.microsoft.com/updates/azure-scheduler-will-retire-on-september-30-2019/)。作为替代方案，可以使用 Azure 逻辑应用创建计划作业。有关从计划程序迁移到逻辑应用的详细步骤，请参阅[将 Azure 计划程序作业迁移到 Azure 逻辑应用](https://docs.azure.cn/zh-cn/scheduler/migrate-from-scheduler-to-logic-apps)。

有关更多信息：
* 通过完成[逻辑应用教程](https://docs.azure.cn/zh-cn/logic-apps/#step-by-step-tutorials)，熟悉 Azure 逻辑应用中的功能。
* 查阅[逻辑应用概述](https://docs.azure.cn/zh-cn/logic-apps/logic-apps-overview)。
 
## 站点恢复

您无法跨 Azure 区域移动现有的 Azure 站点恢复设置。[禁用](https://docs.azure.cn/zh-cn/site-recovery/site-recovery-manage-registration-and-protection)现有配置并在目标 Azure 区域中设置新的站点恢复解决方案。

通过完成这些分步教程来刷新您的知识：
* [从 Azure 到 Azure 的灾难恢复](https://docs.azure.cn/zh-cn/site-recovery/#azure-to-azure)
* [从 Vmware 到 Azure 的灾难恢复](https://docs.azure.cn/zh-cn/site-recovery/#vmware)
* [从 Hyper-V 到 Azure 的灾难恢复](https://docs.azure.cn/zh-cn/site-recovery/#hyper-v)

## Azure门户
(place holder)

## Azure顾问
(place holder)

## 自动化
(place holder)

## Azure监控器
(place holder)

## Azure服务运行状况
(place holder)

## 流量管理器
请参阅[迁移网络资源](china-migration-tools-and-guidance-networking.md)中的**流量管理器**章节。

## 网络观察程序
请参阅[迁移网络资源](china-migration-tools-and-guidance-networking.md)中的**网络观察程序**章节。

## Azure 策略
(place holder)

## Azure 资源管理器
(place holder)

## Azure Resource Graph
(place holder)
