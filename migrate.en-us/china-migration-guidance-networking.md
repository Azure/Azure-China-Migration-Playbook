---
title: Migrate Network Resources
description: This article provides the guidance and tools on migrating nerwork resources.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/09/2019
ms.author: frankch

---

# Migrate network resources

Most networking services don’t support migration across Azure regions. However, you can connect your networks in two cloud environments with [Global VNet Peering](https://docs.azure.cn/virtual-network/virtual-network-peering-overview). Global VNet peering enables you to connect across regions privately using the Microsoft Backbone. Once peered, the virtual networks appear as one for connectivity purposes. The steps you take to set up VNet peering across regions are listed below. Once your virtual networks created, all you need to do is peer them.


>*NOTE*  
>*VVNet Peering will only work when connecting the same cloud environment types. If you’re connecting different cloud environment sites, such as sovereign and public, then use [VPN Gateway](https://docs.azure.cn/vpn-gateway/vpn-gateway-about-vpngateways).*

Here are the summary of the steps needed to create a peering:
1. Create a Virtual Network in the target region.
2. Create a peering link from the new virtual network that is in the target region to the virtual network in the source region.
3. Create a peering link from the virtual network in the source region to the new virtual network created in the target region.

## Virtual networks

Migrating virtual networks across Azure regions isn’t supported at this time. We recommend that you create new virtual networks in the target region and migrate resources into those virtual networks.

For more information: 
* Refresh your knowledge by completing the [Azure Virtual Network tutorials](https://docs.azure.cn/zh-cn/virtual-network/#step-by-step-tutorials).
* Review the [virtual networks overview](https://docs.azure.cn/virtual-network/virtual-networks-overview).
* Learn how to [plan virtual networks](https://docs.azure.cn/virtual-network/virtual-network-vnet-plan-design-arm).
* Learn how to [create a VNet peer](https://docs.azure.cn/virtual-network/virtual-network-peering-overview).
* [How to create VNet peering with different deployment models and subscriptions](https://docs.azure.cn/virtual-network/create-peering-different-deployment-models-subscriptions).
* [How to create VNet peering with different subscriptions.](https://docs.azure.cn/virtual-network/create-peering-different-subscriptions)

## Network security groups

Migrating network security groups across Azure regions isn’t supported at this time. We recommend that you create new network security groups in the target region and apply the network security groups rules to the new application environment.

Get the current configuration of any network security group from the Azure portal or by running the following PowerShell commands:
```PowerShell
$nsg=Get-AzureRmNetworkSecurityGroup -ResourceName <nsg-name> -ResourceGroupName <resourcegroupname>
Get-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg
```
For more information: 
* Refresh your [knowledge about network security groups.](https://docs.azure.cn/virtual-network/)
* Review the [network security overview.](https://docs.azure.cn/virtual-network/)
* Learn how to [manage network security groups.](https://docs.azure.cn/virtual-network/manage-network-security-group)。

## ExpressRoute

Migrating an Azure ExpressRoute instance across Azure regions isn’t supported at this time. For migration across cloud types, we recommend that you create new ExpressRoute circuits and a new ExpressRoute gateway in the target Azure region.

For more information: 
* Refresh your knowledge by completing the [ExpressRoute tutorials.](https://docs.azure.cn/expressroute/#step-by-step-tutorials)
* Learn how to [create a new ExpressRoute gateway.](https://docs.azure.cn/expressroute/expressroute-howto-add-gateway-portal-resource-manager)。
* Learn about [ExpressRoute locations and service providers.](https://docs.azure.cn/expressroute/expressroute-locations)。
* Read about [virtual network gateways for ExpressRoute.](https://docs.azure.cn/zh-cn/expressroute/expressroute-about-virtual-network-gateways)的内容。
 
## VPN Gateway 

Migrating an Azure VPN Gateway instance across Azure regions isn’t supported at this time. We recommend that you create and configure a new instance of VPN Gateway in the new region.
You can collect information about your current VPN Gateway configuration by using the portal or PowerShell. In PowerShell, use a set of cmdlets that begin with  `Get- AzureRmVirtualNetworkGateway `.
Make sure that you update your on-premises configuration. Also, delete any existing rules for the old IP address ranges after you update your Azure network environment.
 

For more information: 
* Refresh your knowledge by completing the [VPN Gateway tutorials.](https://docs.azure.cn/vpn-gateway/#step-by-step-tutorials)
* Learn how to [create a site-to-site connection.](https://docs.azure.cn/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)
* Review the [Get-AzureRmVirtualNetworkGateway PowerShell cmdlet.](https://docs.microsoft.com/zh-cn/powershell/module/azurerm.network/get-azurermvirtualnetworkgateway?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.5.0)
* Read the blog post: [Create a site-to-site connection.](https://blogs.technet.microsoft.com/ralfwi/2017/02/02/connecting-clouds/)。

## Application Gateway

Migrating an Azure Application Gateway instance across Azure regions isn’t supported at this time. We recommend that you create and configure a new gateway in the new region. 
You can collect information about your current gateway configuration by using the portal or PowerShell. In PowerShell, use a set of cmdlets that begin with `Get- AzureRmApplicationGateway`.

For more information: 
* Refresh your knowledge by completing the [Application Gateway tutorials.](https://docs.azure.cn/application-gateway/#step-by-step-tutorials)
* Learn how to [create an application gateway.](https://docs.azure.cn/application-gateway/quick-create-portal)。
* Review the [Application Gateway PowerShell cmdlets.](https://docs.microsoft.com/en-us/powershell/module/azurerm.network/?view=azurermps-6.13.0#application_gateway)

## DNS

To migrate your Azure DNS configuration across Azure regions, export the DNS zone file, and then import it under the new subscription. Currently, the only way to export the zone file is by using the Azure CLI.

After you sign in to your source subscription in the source Azure region, configure the Azure CLI to use Azure Resource Manager mode. Export the zone by running this command:
```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Example:
```azurecli
az network dns zone export -g "myresourcegroup" -n "contoso.com" -f "contoso.com.txt"
```
This command calls the Azure DNS service to export the zone contoso.com in the resource group myresourcegroup. The output is stored as a BIND-compatible zone file in contoso.com.txt in the current folder.

When the export is finished, delete the NS records from the zone file. New NS records are created for the new region and subscription.
Next, sign in to your target environment, create a new resource group (or select an existing one), and then import the zone file:
```azurecli    
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```
When the zone has been imported, you must validate the zone by running the following command:
```azurecli
az network dns record-set list -g <resource group> -z <zone name>
```
When validation is finished, contact your domain registrar and redelegate the NS records. To get NS record information, run the following command:
```azurecli
az network dns record-set ns list -g <resource group> -z --output json
```
For more information: 
* Refresh your knowledge by completing the [Azure DNS tutorials.](https://docs.azure.cn/dns/#step-by-step-tutorials)
* Review the [Azure DNS overview.](https://docs.azure.cn/dns/dns-overview)。
* Learn more about [Azure DNS import and export.](https://docs.azure.cn/dns/dns-import-export)。

## Network Watcher

Migrating a Network Watcher instance across Azure regions isn’t supported at this time. We recommend that you create and configure a new Network Watcher instance in the target region. Afterward, compare results between the old and new environments.
 
For more information: 
* Refresh your knowledge by completing the [Network Watcher tutorials.](https://docs.azure.cn/network-watcher/#step-by-step-tutorials)
* Review the [Network Watcher overview.](https://docs.azure.cn/network-watcher/network-watcher-monitoring-overview)。
* Learn more about [network security group flow logs.](https://docs.azure.cn/network-watcher/network-watcher-nsg-flow-logging-portal)
* Read about [Connection Monitor.](https://docs.azure.cn/network-watcher/connection-monitor)

## Traffic Manager

Azure Traffic Manager can help you complete a smoother migration. While migrating Azure resources across Azure regions, you can add a new target endpoint in the target region, and update the Traffic Manager profile to use the new endpoint.

However, you can’t migrate Traffic Manager profiles across Azure cloud types. You can define additional endpoints in the target environment by creating a new Traffic Manager profile in the target region while using the source environment at the same time. When Traffic Manager is running in the new environment, you can still define endpoints that you haven’t yet migrated in the source environment. This scenario is known as the [Blue- Green scenario.](https://azure.microsoft.com/en-us/blog/blue-green-deployments-using-azure-traffic-manager/)

The scenario involves the following steps:
1. Create a new Traffic Manager profile in the target Azure region.
2. Migrate and configure endpoints. For each endpoint in the source Azure region: 
    1. Migrate the endpoint to the target Azure region.
    2. Add the endpoint in the new Traffic Manager profile. 
3. Change your DNS CNAME record to the new Traffic Manager profile.
4.	Wait until the queries to the old ATM profiles totally stop by monitoring the [Queries by endpoint returned](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-metrics-alerts) metric. Some LDNS might have cached the old profile names – it’s a good idea to wait for some time to make sure all queries are now routed to the new ATM profile before disabling the old profile.
5. Disable the old Traffic Manager profile
6. Once you’re sure the old ATM profile can be safely deleted, delete the old ATM profile.

For more information: 
* Refresh your knowledge by completing the [Traffic Manager tutorials.](https://docs.azure.cn/traffic-manager/#step-by-step-tutorials)
* Review the [Traffic Manager overview.](https://docs.azure.cn/traffic-manager/traffic-manager-overview)。
* Learn how to [create a Traffic Manager profile.](https://docs.azure.cn/traffic-manager/quickstart-create-traffic-manager-profile)。

## Load Balancer

Migrating a Load Balancer instance across Azure regions isn’t supported at this time. We recommend that you create and configure a new load balancer in the target Azure region. 
If you’re currently using the [Azure Load Balancer - **Basic**](https://docs.azure.cn/load-balancer/quickstart-create-basic-load-balancer-portal), it is recommended to upgrade to the [Azure Load Balancer – **Standard**](https://docs.azure.cn/zh-cn/load-balancer/quickstart-load-balancer-standard-public-portal).

Learn more about [Why use Standard Load Balancer](https://docs.azure.cn/load-balancer/load-balancer-standard-overview#why-use-standard-load-balancer), including [limits](https://docs.azure.cn/azure-subscription-service-limits#load-balancer) and [pricing](https://www.azure.cn/pricing/details/load-balancer/).


>*NOTE*
>*As we continue to add new capabilities and features for the Load Balancer, we anticipate they’ll only be available on the Standard SKU.*

For more information: 
* Refresh your knowledge by completing the [Load Balancer tutorials.](https://docs.azure.cn/load-balancer/quickstart-create-basic-load-balancer-portal)
* Review the [Load Balancer overview.](https://docs.azure.cn/load-balancer/load-balancer-overview)
* Learn how to [create a new load balancer.](https://docs.azure.cn/load-balancer/quickstart-load-balancer-standard-public-portal)

## CDN 
(place holder)

## Cloud Connection Service
(place holder)

