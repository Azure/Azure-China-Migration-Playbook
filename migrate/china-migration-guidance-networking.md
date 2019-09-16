---
title: 迁移网络资源
description: This article provides the guidance and tools on migrating nerwork resources.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/06/2019
ms.author: frankch

---

# 迁移网络资源

大多数网络服务不支持跨 Azure 区域的迁移。但是，您可以使用[全局 Vnet 对等互连](https://docs.azure.cn/zh-cn/virtual-network/virtual-network-peering-overview)将您的网络连接到两个云环境中。全局 Vnet 对等互连让您可以使用 Microsoft Backbone 以私有的方式将区域利用主干网互联。完成对等互连后，虚拟网络就会出现以进行连接。下面列出了跨区域设置 VNet 对等互连的步骤。一旦新的虚拟网络创建后，您只需要将其进行对等互连。

>*注意*  
>*Vnet 对等互连仅在连接相同的云环境类型时才起作用。如果您要连接不同的云环境站点，例如主权站点和公共站点，请使用 [VPN 网关](https://docs.azure.cn/zh-cn/vpn-gateway/vpn-gateway-about-vpngateways)。*

以下是创建对等互连所需步骤的摘要：
1. 在目标区域中创建虚拟网络。
2. 创建从目标区域中的新虚拟网络到源区域中的虚拟网络的对等互连链路。
3. 创建从源区域中的虚拟网络到目标区域中创建的新虚拟网络的对等互连链路。

## 虚拟网络

目前不支持跨 Azure 区域迁移虚拟网络。我们建议您在目标区域中创建新的虚拟网络，并将资源迁移到这些虚拟网络中。

有关更多信息：
* 通过完成 [Azure 虚拟网络教程](https://docs.azure.cn/zh-cn/virtual-network/#step-by-step-tutorials)来刷新您的知识。
* 查阅[虚拟网络概述](https://docs.azure.cn/zh-cn/virtual-network/virtual-networks-overview)。
* 了解如何[规划虚拟网络](https://docs.azure.cn/zh-cn/virtual-network/virtual-network-vnet-plan-design-arm)。
* 了解如何[创建 Vnet 对等互连](https://docs.azure.cn/zh-cn/virtual-network/virtual-network-peering-overview)。
* [如何使用不同的部署模型和订阅创建 Vnet 对等互连](https://docs.azure.cn/zh-cn/virtual-network/create-peering-different-deployment-models-subscriptions)。
* [如何使用不同的订阅创建 Vnet 对等互连](https://docs.azure.cn/zh-cn/virtual-network/create-peering-different-subscriptions)。

## 网络安全组

目前不支持跨 Azure 区域迁移网络安全组。我们建议您在目标区域中创建新的网络安全组，并将网络安全组规则应用于新的应用程序环境。
可以通过 Azure 门户获取任何网络安全组的当前配置，或者运行以下 PowerShell 命令以获取配置：
```PowerShell
$nsg=Get-AzureRmNetworkSecurityGroup -ResourceName <nsg-name> -ResourceGroupName <resourcegroupname>
Get-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg
```
有关更多信息：
* 刷新您对[网络安全组](https://docs.azure.cn/zh-cn/virtual-network/)的了解。
* 查阅[网络安全概述](https://docs.azure.cn/zh-cn/virtual-network/)
* 了解如何[管理网络安全组](https://docs.azure.cn/zh-cn/virtual-network/manage-network-security-group)。

## ExpressRoute

目前不支持跨 Azure 区域迁移 Azure ExpressRoute 实例。对于跨云类型的迁移，我们建议您在目标 Azure 区域中创建新的 ExpressRoute 线路和新的 ExpressRoute 网关。

有关更多信息：
* 通过完成 [ExpressRoute 教程](https://docs.azure.cn/zh-cn/expressroute/#step-by-step-tutorials)来刷新您的知识。
* 了解如何[创建新的 ExpressRoute 网关](https://docs.azure.cn/zh-cn/expressroute/expressroute-howto-add-gateway-portal-resource-manager)。
* 了解 [ExpressRoute 位置和服务提供商](https://docs.azure.cn/zh-cn/expressroute/expressroute-locations)。
* 阅读有关 [ExpressRoute 的虚拟网络网关](https://docs.azure.cn/zh-cn/expressroute/expressroute-about-virtual-network-gateways)的内容。
 
## VPN 网关

目前不支持跨 Azure 区域迁移 Azure VPN 网关实例。我们建议您在新区域中创建和配置 VPN 网关的新实例。  
您可以使用门户网站或 PowerShell 收集有关当前 VPN 网关配置的信息。在 PowerShell 中，使用以 `Get-AbureRmVirtualNetworkGateway` 开头的一组 cmdlet。  
确保更新您的本地配置。此外，在更新 Azure 网络环境后，删除旧 IP 地址范围内的任何现有规则。

有关更多信息：
* 通过完成 [VPN 网关教程](https://docs.azure.cn/zh-cn/vpn-gateway/#step-by-step-tutorials)来刷新您的知识。
* 了解如何[创建站点到站点的连接](https://docs.azure.cn/zh-cn/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)。
* 查阅 [Get-AzureRmVirtualNetworkGateway PowerShell cmdlet](https://docs.microsoft.com/zh-cn/powershell/module/azurerm.network/get-azurermvirtualnetworkgateway?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.5.0)。
* 阅读博客文章：[创建站点到站点的连接](https://blogs.technet.microsoft.com/ralfwi/2017/02/02/connecting-clouds/)。

## 应用程序网关

目前不支持跨 Azure 区域迁移 Azure 应用程序网关实例。我们建议您在新区域中创建和配置新网关。  
您可以使用门户网站或 PowerShell 收集有关当前网关配置的信息。在 PowerShell 中，使用以 Get- AzureRmApplicationGateway 开头的一组 cmdlet。

有关更多信息：
* 通过完成[应用程序网关教程](https://docs.azure.cn/zh-cn/application-gateway/#step-by-step-tutorials)来刷新您的知识。
* 了解如何[创建应用程序网关](https://docs.azure.cn/zh-cn/application-gateway/quick-create-portal)。
* 查阅[应用程序网关 PowerShell cmdlet](https://docs.microsoft.com/en-us/powershell/module/azurerm.network/?view=azurermps-6.13.0#application_gateway)。

## DNS

要在 Azure 区域间迁移 Azure DNS 配置，首先需要从源区域中导出 DNS 区域文件，然后在目标区域新建 DNS 区域并导入它。目前，导出 DNS 区域文件的唯一方法是使用 Azure CLI。  
登录 Azure ，将 Azure CLI 配置为使用 Azure 资源管理器模式。通过运行以下命令导出区域：
```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```
示例:
```azurecli
az network dns zone export -g "myresourcegroup" -n "contoso.com" -f "contoso.com.txt"
```
此命令调用 DNS 服务以导出资源组 myresourcegroup 中的 DNS 区域 contoso.com。输出作为一个 BIND 兼容的 DNS 区域文件存储在当前文件夹的 contoso.com.txt 文件中。
导出完成后，从 DNS 区域文件中删除 NS 记录，并为新 Azure 区域创建新的 NS 记录。
接下来，创建新资源组（或选择现有资源组），然后导入 DNS 区域文件：
```azurecli    
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```
DNS 区域导入完成后，您需要通过运行以下命令来验证 DNS 区域：
```azurecli
az network dns record-set list -g <resource group> -z <zone name>
```
验证完成后，请与您的域名注册商联系并重新授权 NS 记录。要获取 NS 记录信息，请运行以下命令：
```azurecli
az network dns record-set ns list -g <resource group> -z <zone name> --output json
```
有关更多信息：
* 通过完成 [Azure DNS 教程](https://docs.azure.cn/zh-cn/dns/#step-by-step-tutorials)来刷新您的知识。
* 查阅 [Azure DNS 概述](https://docs.azure.cn/zh-cn/dns/dns-overview)。
* 了解有关 [Azure DNS 导入和导出的更多信息](https://docs.azure.cn/zh-cn/dns/dns-import-export)。

## 网络观察程序

目前不支持跨 Azure 区域迁移网络观察程序实例。我们建议您在目标区域中创建和配置新网络观察程序。然后，比较旧环境和新环境之间的结果。
 
有关更多信息：
* 通过完成[网络观察程序教程](https://docs.azure.cn/zh-cn/network-watcher/#step-by-step-tutorials)来刷新您的知识。
* 查阅[网络观察程序概述](https://docs.azure.cn/zh-cn/network-watcher/network-watcher-monitoring-overview)。
* 了解有关[网络安全组流日志](https://docs.azure.cn/zh-cn/network-watcher/network-watcher-nsg-flow-logging-portal)的更多信息。
* 阅读有关[连接监视器](https://docs.azure.cn/zh-cn/network-watcher/connection-monitor)的内容。

## 流量管理器

Azure 流量管理器可帮助您更顺畅地完成迁移。在 Azure 区域中迁移 Azure 资源时，可以在目标区域中添加新的目标端点，并更新流量管理器配置文件以使用新端点。  
但是，您无法跨 Azure 云类型迁移流量管理器配置文件。通过在目标区域中创建新的流量管理器配置文件，您可以在目标环境中定义额外的端点，同时还可以使用源环境。当流量管理器在新环境中运行时，您仍可以在源环境中定义尚未迁移的端点。此场景被称为[蓝绿部署](https://azure.microsoft.com/en-us/blog/blue-green-deployments-using-azure-traffic-manager/)场景。

此场景涉及以下步骤：
1. 在目标 Azure 区域中创建新的流量管理器配置文件。
2. 迁移和配置端点。对于源 Azure 区域中的每个端点：
    1. 将端点迁移到目标 Azure 区域。
    2. 将端点添加到新的流量管理器配置文件中。
3. 将 DNS CNAME 记录更改为新的流量管理器配置文件。
4. 通过[查询端点返回的监控指标](https://docs.microsoft.com/zh-cn/azure/traffic-manager/traffic-manager-metrics-alerts)，监控对旧 ATM 配置文件的查询是否完全停止。有些 LDNS 可能已经缓存了旧的配置文件名称 - 在禁用旧配置文件之前，最好等待一段时间以确保所有查询现在都路由到新的 ATM 配置文件。
5. 禁用旧的流量管理器配置文件
6. 一旦确定可以安全删除旧的 ATM 配置文件，则立即删除它。

有关更多信息：
* 通过完成[流量管理器教程](https://docs.azure.cn/zh-cn/traffic-manager/#step-by-step-tutorials)来刷新您的知识。
* 查阅[流量管理器概述](https://docs.azure.cn/zh-cn/traffic-manager/traffic-manager-overview)。
* 了解如何[创建流量管理器配置文件](https://docs.azure.cn/zh-cn/traffic-manager/quickstart-create-traffic-manager-profile)。

## 负载均衡器

目前不支持跨 Azure 区域迁移负载均衡器实例。我们建议您在目标 Azure 区域中创建和配置新的负载均衡器。  
如果您当前正在使用 [Azure负载均衡器 - **基础版**](https://docs.azure.cn/zh-cn/load-balancer/quickstart-create-basic-load-balancer-portal)，则建议您升级到 [Azure 负载均衡器 - **标准版**](https://docs.azure.cn/zh-cn/load-balancer/quickstart-load-balancer-standard-public-portal)。  
了解有关[为何使用标准负载均衡器](https://docs.azure.cn/zh-cn/load-balancer/load-balancer-standard-overview#why-use-standard-load-balancer)的更多信息，包括[限制](https://docs.azure.cn/zh-cn/azure-subscription-service-limits#load-balancer)和[定价](https://www.azure.cn/zh-cn/pricing/details/load-balancer/)。

>*注意：*
>*由于我们继续为负载均衡器添加新的功能和特性，我们预计它们只能在标准 SKU 上使用。*

有关更多信息：
* 通过完成[负载均衡器教程](https://docs.azure.cn/zh-cn/load-balancer/quickstart-create-basic-load-balancer-portal)来刷新您的知识。
* 查阅[负载均衡器概述](https://docs.azure.cn/zh-cn/load-balancer/load-balancer-overview)。
* 了解如何[创建新的负载均衡器](https://docs.azure.cn/zh-cn/load-balancer/quickstart-load-balancer-standard-public-portal)。

## CDN 内容分发网络

CDN 内容分发网络功能没有区域属性，不需要进行迁移。

## 云连接服务

云连接服务网络功能没有区域属性，不需要进行迁移。

