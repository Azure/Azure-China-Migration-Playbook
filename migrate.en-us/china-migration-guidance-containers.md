---
title: Migrate Container Resources
description: This article provides the guidance and tools on migrating container resources.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/09/2019
ms.author: frankch

---


# Migrate Container Resources
This section contains information that you may find helpful as you migrate Azure container resources across Azure regions.

## Azure Container Registry

To migrate Azure Container Registry across Azure regions, use [Geo-Replication](https://docs.azure.cn/zh-cn/container-registry/container-registry-geo-replication). However, Geo-replication doesn’t work when you have to migrate Azure Container Registry across cloud types. If you want to move Azure Container Registry instance across cloud types, then create a new container registry in the target region and use the [import API](https://docs.azure.cn/container-registry/container-registry-import-images) to import container image to the new registry created.


## Service Fabric

See **Service Fabric** chapter of [Migrate Cpmpute Resources](./china-migration-guidance-compute.md).

## Batch

See **Batch** chapter of [Migrate Cpmpute Resources](./china-migration-guidance-compute.md).

## App Service
(place holder)
(Web章节也空缺中)

## Azure Kubernetes Service(AKS)
(place holder)
(compute章节也空缺中)
