---
title: Azure资源跨区域迁移手册
description: This article provides the guidance and tools on migrating resources between different Azure China regions.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/26/2019
ms.author: frankch

---

# Azure资源跨区域迁移手册

本章节中描述了各迁移阶段可以利用的工具及相关的迁移步骤。

## 资源评估工具

资源评估工具用于：
* 了解源环境和目的环境之间服务与配置的一致性
* 估算在目的环境中的运营成本
* 了解已部署资源之前的依赖关系
* 为决策和工作计划生成综合评估报告

由于 Azure 中国的主权云属性，其迁移和扩展在互联网环境和政策方面独具复杂性。用户可以使用开源项目[**迁移评估工具**](https://github.com/Azure/ccme)来评估源环境和目的环境之间服务与配置的一致性并估算迁移成本，并提供了相关迁移工具建议和视频指南。  
在使用迁移评估工具时，用户需先登录，输入用于评估的订阅信息以查看评估报告。  
迁移指南已经过工程验证，并为主要Azure资源迁移提供循序渐进的视频指南。主要资源包括虚拟机、存储、虚拟网络、数据库、Web 应用和 Azure Cosmos DB。  

Azure MSP 合作伙伴提供了更多相关工具供客户选择，相关产品描述请参阅[合作伙伴介绍](./china-migration-partners.md)的相关章节。

## 迁移计划工具

迁移计划工具用于：
* 根据资源之间的依赖关系，规划资源的迁移顺序
* 根据资源的迁移顺序，估算迁移所需时间
* 生成迁移计划报告
>*注意:*  
>*迁移工具生成的迁移计划报告不能直接用于资源迁移，需经过与客户的架构师和开发团队讨论并最终确认。*  

目前 Azure 没有提供自动化工具用于生成迁移计划，Azure MSP 合作伙伴提供了相关工具供客户选择，相关产品描述请参阅[合作伙伴介绍](./china-migration-partners.md)的相关章节。

## 资源迁移方法

资源迁移必须依据用户最终确认的迁移计划，顺序依次迁移所部署的资源。

* [迁移安全资源](./china-migration-guidance-security.md)

* [迁移标识资源](./china-migration-guidance-identity.md)

* [迁移存储资源](./china-migration-guidance-storage.md)

* [迁移分析服务](./china-migration-guidance-analytics.md)

* [迁移管理工具资源](./china-migration-guidance-management-tools.md)

* [迁移集成资源](./china-migration-guidance-integration.md)

* [迁移计算资源](./china-migration-guidance-compute.md)
  
* [迁移网络资源](./china-migration-guidance-networking.md)

* [迁移媒体资源](./china-migration-guidance-media.md)

* [迁移容器资源](./china-migration-guidance-containers.md)

* [迁移数据库资源](./china-migration-guidance-databases.md)

* [迁移 Web 资源](./china-migration-guidance-web.md)

* [迁移 IoT 资源](./china-migration-guidance-iot.md)

* [迁移移动资源](./china-migration-guidance-mobile.md)

## 合作伙伴选择

经过微软认证的 Azure MSP 合作伙伴可以为 Azure 客户提供完善的一站式跨 Azure 区域迁移服务。具体的介绍请查阅[合作伙伴选择](./china-migration-partners.md)章节的介绍。
