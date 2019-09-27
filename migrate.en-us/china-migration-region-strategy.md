---
title: Region Strategy
description: This article describes the characteristics of different regions of Azure China and can help you choose the applicable region for your company.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/26/2019
ms.author: frankch

---


# Region Strategy

  
In China, [**Microsoft Azure** operated by 21Vianet](https://www.azure.cn/en-us/home/features/what-is-azure/) is an independently managed, publicly-owned cloud platform on Mainland China and is physically and logically independent of Microsoft Cloud-managed Azure services in other regions worldwide. It uses Microsoft global Azure technology to provide clients globally consistent services with guaranteed quality. Currently, there are two top data centers deployed in the east (Shanghai) and the north (Beijing).

Theoretically, cloud computing resources can be regarded as nearly infinite under certain conditions. However, due to the rapid development of cloud computing technology and the rapid growth of China's Internet business, the construction speed and construction mode of cloud service infrastructure need to be dynamically adjusted according to the demand constantly to meet the growing and changing business needs. For the potential risks brought by this, it is necessary for users to fully understand the characteristics of cloud services and adapt and adjust the system built based on cloud services to improve the flexibility and stability of the system.


## Regional characteristic difference

China's cloud customers are in regional distribution and different dimensions of industry/product/service/IT systems cycle , such as the demand has the very big difference. So China cloud regions in the adjustment of the iteration, according to customer's requirements also turn in and out of the unique characteristics of the corresponding regions so as to adapt to China's cloud the regional business development needs of clients.

Understanding these differences and making better use of them in the process of system landing, expansion, upgrade and reconstruction can help the system architecture to realize long-term adaptation to the deployed area in design, and reduce the potential impact on customers due to various external factors.


### Regional service differences

According to the characteristics and needs of customers in Azure China, the deployment schedule of specific services in each region will also be different. For specific differences, please refer to [Azure Services Availability](https://www.azure.cn/zh-cn/home/features/products-by-region). The list is daily updated to ensure that users are kept up to date.In addition, access [Latest announcements from Azure](https://www.azure.cn/zh-cn/what-is-new/) where full announcements of Azure's new service launch are available

### Industry distribution difference

Because the business or users of some industry customers will be concentrated in some hot region or time periods, the demand for resources in these regions or time periods will be concentrated and released, resulting in resource strain.

* Retail industry: Retail industry is one of the key industries Azure cloud business in China, especially in recent years the rapid growth of China's consumer market and the vigorous development of the new retail related business, so both in domestic enterprises and overseas enterprises regard China as a global important consumer market development, especially in eastern China in the south of Yangtze river delta and pearl river delta region, is the key business development area of retail enterprises. Therefore, among cloud resources deployed by public cloud service enterprises in southeast China, retail customers are all important resource consumers. Especially in the hot activity season of the retail industry, such as "double 11", it is the concentrated release time for the demand of various enterprises for cloud services, which leads to the shortage of resources in these periods.

### Premium resource distribution differences

For premium resources such as SSD, GPU server, etc., due to the relatively high input in the early stage, the speed of resource input is based on the analysis of the potential business demand in each region, and then the investment is made in stages according to the prediction of the growth trend.Therefore, compared with ordinary resources, the price of advanced resources will be relatively high and the quantity of resources will be less than ordinary resources.Therefore, the correct use of advanced resources can effectively reduce operating costs and improve the robustness of the system.

* Premium SSD: Premium SSD is based on high-performance SSD storage resource hardware facilities. If they are used intensively in some areas, the resources will be relatively scarce for a period of time, which will affect the business development of customers in that area.
* GPU: GPU resources are already online in China North2 and China East2. If the user has a need for GPU resources, you can deploy your system in these two regions to improve the overall system performance.  

More Information：

* See [Microsoft Azure operated by 21Vianet vs. Global Azure ](https://www.microsoft.com/china/azure/index.html).

## Optimize resources of used region

If your system deployed on Azure has the pitfalls described above, you can adjust the rules described below to reduce or eliminate the impact of the pitfalls.

### Make proper use of hot regions

For business application scenarios where Azure Chinese cloud users are not concentrated in the China East region and the delay requirements are not high, the resources of 2 regions in the china north should be used as much as possible to reduce the potential impact of resource shortage caused by hot industry activities on your business.

### Make proper use of hot resources

For premium resources, use them appropriately based on the specific business metrics of your business.Unless the general resources can not meet the business requirements, try to avoid the use of advanced resources in unnecessary scenarios, not only improve the robustness of the system, but also reduce the system operating costs.
    

For Example：
* When creating VM resources,  if normal HDDS meet the metrics, you can select **standard HDD**  instead of the default option **advanced SSD** in the **OS disk type** to reduce dependence on advanced managed disks.
* When creating a snapshot, you can select **standard HDD** in stead of **premium SSD** in **account type** selection.

More Imformation：
* See [Managed disks pricing details](https://www.azure.cn/en-us/pricing/details/storage/managed-disks/) for more information about the metrics and price differences between standard HDDS and premium SSDS.

## Azure China Migaration

If you want to reduce or eliminate hazards over the long term by migrating systems across regions, see [Migration Process](china-migration-process.md).

## Contact us

>If you would like more advice on region selection and optimization, please contact us (chinamigrationpb@microsoft.com) for more details.

