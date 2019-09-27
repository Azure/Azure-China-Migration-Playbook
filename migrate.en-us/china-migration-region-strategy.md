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

  
In China, [**Microsoft Azure** operated by 21Vianet](https://azure.microsoft.com/en-us/global-infrastructure/china/) is an independently managed, public cloud platform in China, and is physically and logically independent of Microsoft cloud-based Azure services in other regions worldwide. It uses Microsoft Azure technology to provide clients globally consistent services with guaranteed quality. Currently, there are two top data centers positioned in the east (Shanghai) and two in the north (Beijing).

Theoretically, cloud computing resources can be regarded as nearly infinite under certain conditions. However, due to the rapid development of cloud computing technology and the rapid growth of China's internet business, the construction speed and construction mode of cloud service infrastructure need to be dynamically adjusted according to the demand, constantly catering to the growing and forever-changing business needs. For the potential risks brought by this, it is necessary for users to fully understand the characteristics of cloud services, adapting and adjusting the system built based on cloud services to improve the flexibility and stability.


## Regional characteristic difference

China's cloud customers in different geos and different dimensions of industry/product/service/IT systems cycle , have very different demand. Therefore, while China cloud regions upgrade in iterations according to customer's requirements, they also gradually develop unique characteristics in different regions, catering to business development demands of customers in different china cloud regions.

Understanding these differences and better leveraging them in the process of system landing, expansion, upgrade and reconstruction can contribute to system architecture being long-term adapted to the deployed region from design phase, and reduce the potential risk to customers while various external factors change.


### Regional service differences

According to the characteristics and needs of customers in Azure China, the deployment roadmap of specific services in each region is different too. For service parity, please refer to [Azure Services Availability](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=all&regions=non-regional,china-non-regional,china-east,china-east-2,china-north,china-north-2). The list is up-to-date, ensuring customer to receive latest information. In addition, access [Azure Updates](https://azure.microsoft.com/en-us/updates/) to learn about important Azure product updates, roadmap, and announcements.

### Industry distribution difference

Because the business traffic or visitors of some industries will be concentrated in some hot regions or time periods, the demand for resources in these regions or time periods will be concentrated, which leads to resource shortage.

* Retail industry: Retail industry is one of the key industries cloud business focusing, especially in recent years with rapid growth of China's consumer market and vigorous development of new retail business, not only domestic enterprises but also overseas enterprises develop China market as an important consumer market in global, especially Yangtze river delta of China East and pearl river delta region of China North, are the key business development areas to retail business. Therefore, among public cloud services customers of southeast China, customers of retail business are all key resource consumers. Especially in the hot season of retail industry, such as "double 11", enterprise customers will release demands on public cloud services concentratedly, which leads to the shortage of resources in these periods.

### Premium resource distribution differences

For premium resources such as SSD, GPU server, etc., due to the relatively high investment in the early stage, the roadmap of the resources is based on the analysis of the potential business demand of each region, and then the investment is placed in stages according to the prediction of growth. Therefore, compared with general resources, the price of advanced resources will be relatively high, and the quantity of resources will be limited. However, reasonable using of advanced resources can effectively reduce operating costs and improve the robustness of the system.

* Premium SSD: Premium SSD is based on high-performance SSD storage resource hardware facilities. If they are used intensively in some areas, the resources will be relatively scarce for a period of time, which will affect the business development of customers in that area.
* GPU: GPU resources are already online in China North2 and China East2. If the user has a need for GPU resources, you can deploy your system in these two regions to improve the overall system performance.  

More Information：

* See [Microsoft Azure operated by 21Vianet vs. Global Azure ](https://www.microsoft.com/china/azure/index.html).

## Optimize resources of used region

You can adjust your deployment according to the rules described below to mitigate or eliminate the portential risk.

### Make proper use of hot regions

For business scenarios with wide-spreading customers not concentrated in the China East, low request on network latency, the Azure cloud resources of 2 regions in China North are recommended to avoid possible impact from resource constraint because of hot activites of hot industries.

### Make proper use of hot resources

For premium resources, using them appropriately base on specific business metrics of your business. Unless the general resources cannot meet business requirements, avoid using premium resources in unnecessary scenarios, which can not only improve the robustness of system, but also reduce system operating costs.
    

For Example：
* When creating VM resources,  if normal HDDS meet the metrics, you can select **standard HDD**  instead of the default option **advanced SSD** in the **OS disk type** to reduce dependence on advanced managed disks.
* When creating a snapshot, you can select **standard HDD** in stead of **premium SSD** in **account type** selection.

More Imformation：
* See [Managed disks pricing details](https://www.azure.cn/en-us/pricing/details/storage/managed-disks/) for more information about the metrics and price differences between standard HDDS and premium SSDS.

## Azure China Migration

If you want to reduce or eliminate hazards over the long term by migrating systems across regions, see [Migration Process](china-migration-process.md).

## Contact us

If you would like to have more advices on region selection and optimization, please contact us (chinamigrationpb@microsoft.com).


