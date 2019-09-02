# Azure资源跨区域迁移手册
  
## 计算资源迁移
本节提供的信息可帮助您将已部署Azure计算资源从一个Azure区域迁移到其他Azure区域。
  
### 虚拟机  
由于目前中国所有Azure区域都处于[Azure 站点恢复（ASR）](https://docs.azure.cn/zh-cn/site-recovery/site-recovery-overview)所支持的相同地理集群（关于地理群集，[请参阅](https://docs.azure.cn/zh-cn/site-recovery/azure-to-azure-support-matrix#region-support))。可以参照[迁移Azure虚拟机到另一个区域](https://docs.azure.cn/zh-cn/site-recovery/azure-to-azure-tutorial-migrate)所描述的步骤完成虚拟机的迁移。
  
### 云服务  
目前还不支持将云服务从一个 Azure 区迁移到另一个区域。您可以利用 .cspkg 和 .cscfg 定义来重新部署 Azure 云服务资源到另一个区域。
  
#### 利用Azure 门户  
要在 Azure 门户中重新部署云服务，请执行以下操作：
1. 使用 .cspkg 和 .cscfg 定义[创建新的云服务](https://docs.azure.cn/zh-cn/cloud-services/cloud-services-how-to-create-deploy-portal)。
2. 更新[CNAME 或 A 记录](https://docs.azure.cn/zh-cn/cloud-services/cloud-services-custom-domain-name-portal)以将流量导向新的云服务。
3. 当流量导向新的云服务后，删除源区域中的旧云服务。
  
#### 利用PowerShell  
要使用 PowerShell 重新部署云服务，请执行以下操作：
1. 使用 .cspkg 和 .cscfg 定义[创建新的云服务](https://docs.microsoft.com/zh-cn/powershell/module/servicemanagement/azure/new-azureservice)。
```PowerShell
New-AzureService -ServiceName <yourServiceName> -Label <MyTestService> -Location <targetRegion>  
```
2. 使用 .cspkg 和 .cscfg 定义[创建新的部署](https://docs.microsoft.com/zh-cn/powershell/module/servicemanagement/azure/new-azuredeployment)。  
```PowerShell
New-AzureDeployment -ServiceName <yourServiceName> -Slot <Production> -Package <YourCspkgFile.cspkg> -Configuration <YourConfigFile.cscfg>  
```
3. 更新[CNAME 或 A 记录](https://docs.azure.cn/zh-cn/cloud-services/cloud-services-custom-domain-name-portal)以将访问导向新的云服务。
4. 当访问导向新的云服务后，[删除源 Azure 区域中的旧云服务](https://docs.microsoft.com/zh-cn/powershell/module/servicemanagement/azure/remove-azureservice)。  
```PowerShell
Remove-AzureService -ServiceName <yourOldServiceName>
```
  
#### 利用REST API  
要使用 REST API 重新部署云服务，请执行以下操作：
1. 在目标环境中[创建新的云服务](https://docs.microsoft.com/zh-cn/rest/api/compute/cloudservices/rest-create-cloud-service)。
```
https://management.core.windows.net/<subscription-id>/services/hostedservices  
```
2. 使用[创建部署 API](https://msdn.microsoft.com/library/azure/ee460813.aspx)创建新的部署。要获取您的 .cspkg 和 .cscfg 定义，可以调用[Get Package API](https://docs.microsoft.com/en-us/previous-versions/azure/reference/jj154121(v=azure.100))。 
```
https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deploymentslots/production  
```
3. 当流量指向新的云服务时，[删除源 Azure 区域中的旧云服务](https://docs.microsoft.com/zh-cn/rest/api/compute/cloudservices/rest-delete-cloud-service)。  
```
https://management.core.windows.net/<subscription-id>/services/hostedservices/<old-cloudservice-name>
```
  
更多相关信息：
* 请参考[Azure云服务概述](https://docs.azure.cn/zh-cn/cloud-services/cloud-services-choose-me)。
  
### Service Fabric  
要将 Azure Service Fabric 资源从一个 Azure 区域迁移到另一个 Azure 区域，您需要在新区域中创建和重新部署 Service Fabric 群集和应用程序资源。必须备份旧群集中的数据，并将其还原到新群集以保证业务的运行状态。
1. 阅读[生产准备清单](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-production-readiness-checklist)中推荐的指南，使用[Azure 门户](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-cluster-creation-via-portal)或资源管理器资源在目标 Azure 区域的资源组中创建新群集。
2. 不应该出现新的调用，也不应该有任何服务相互通信或执行工作。
3. 如果它是**有状态服务**，则需要将数据从旧群集移动到新群集。请参阅[从旧群集备份数据](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster)，以了解如何将数据从旧群集移动到新群集的信息。
4. 如果它是**无状态服务**，则不需要移动数据，但您必须重新配置流量。
5. 更新要部署到新区域的应用程序配置。
6. [使用资源管理器资源将应用程序部署到新区域](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-application-arm-resource)。
7. 对于**有状态服务**，您需要使用上述步骤 #3 中的备份[还原数据](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-reliable-services-backup-restore#restore-reliable-services)。
8. 更新流量管理器服务（**Azure 流量管理器**）以将流量路由到目标区域。
9. 验证应用程序是否已部署到目标 Azure 区域并接受流量。
10. 验证没有流量流向源 Azure 区域。 
11. [通过清除与源 Azure 区域中的群集相关联的资源组删除群集](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-tutorial-delete-cluster#delete-the-resource-group-containing-the-service-fabric-cluster)。

参考文献：
* [创建 Azure Windows Service Fabric 群集](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-quickstart-containers)
* [创建 Linux Azure Linux Service Fabric 群集](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-quickstart-containers-linux)
* [使用 PowerShell 部署应用程序](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-deploy-remove-applications)

有关更多信息：
* 通过完成[Service Fabric教程](https://docs.azure.cn/zh-cn/service-fabric/#step-by-step-tutorials)来刷新您的知识。
* 了解如何[创建新群集](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-cluster-creation-via-portal)。
* 查阅[Service Fabric概述](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-overview)。

### 批处理

您无法自动将 Azure 批处理帐户和数据从一个区域迁移到另一个区域。如需迁移，您需要完成以下步骤：
1. 在目标区域中[创建批处理帐户](https://docs.azure.cn/zh-cn/batch/batch-account-create-portal)。确保目标区域中有存储帐户，或在目标区域创建存储帐户。
2. 将工作负载部署到新的批处理帐户并开始在那里运行作业。

有关更多信息：
* 通过完成[批处理教程](https://docs.azure.cn/zh-cn/batch/#step-by-step-tutorials)来刷新您的知识。
* 查阅[批处理概述](https://docs.azure.cn/zh-cn/batch/batch-technical-overview)。

### 虚拟机规模集

要跨 Azure 区域迁移虚拟机规模集，请导出资源管理器模板，根据新环境对其进行调整，然后重新部署到目标区域。只需导出基本模板并在新环境中重新部署模板。单个虚拟机规模集实例应该都相同。在开始重新部署之前，请确保了解其他资源的依赖关系并将其迁移到目标区域。

*重要事项*:
*更改位置、密钥保管库密码、证书和其他 GUID 以与新区域保持一致。*
 
有关更多信息：
* 通过完成[虚拟机规模集教程](https://docs.azure.cn/zh-cn/virtual-machine-scale-sets/#step-by-step-tutorials)来刷新您的知识。
* 了解如何[导出 Azure 资源管理器模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)。
* 查阅[Azure资源管理器概述](https://docs.azure.cn/zh-cn/azure-resource-manager/resource-group-overview)。
* 获取[虚拟机规模集概述](https://docs.azure.cn/zh-cn/virtual-machine-scale-sets/overview)。
* 阅读[Azure区域概述](https://www.azure.cn/zh-cn/home/features/products-by-region)。
* 了解如何[重新部署模板](https://docs.azure.cn/zh-cn/azure-resource-manager/resource-group-template-deploy)。

## 迁移网络资源

大多数网络服务不支持跨 Azure 区域的迁移。但是，您可以使用[全局 Vnet 对等互连](https://docs.azure.cn/zh-cn/virtual-network/virtual-network-peering-overview)将您的网络连接到两个云环境中。全局 Vnet 对等互连让您可以使用 Microsoft Backbone 以私有的方式将区域利用主干网互联。完成对等互连后，虚拟网络就会出现以进行连接。下面列出了跨区域设置 VNet 对等互连的步骤。一旦新的虚拟网络创建后，您只需要将其进行对等互连。

*注意*
*Vnet 对等互连仅在连接相同的云环境类型时才起作用。如果您要连接不同的云环境站点，例如主权站点和公共站点，请使用 [VPN 网关](https://docs.azure.cn/zh-cn/vpn-gateway/vpn-gateway-about-vpngateways)。*

以下是创建对等互连所需步骤的摘要：
1. 在目标区域中创建虚拟网络。
2. 创建从目标区域中的新虚拟网络到源区域中的虚拟网络的对等互连链路。
3. 创建从源区域中的虚拟网络到目标区域中创建的新虚拟网络的对等互连链路。

### 虚拟网络

目前不支持跨 Azure 区域迁移虚拟网络。我们建议您在目标区域中创建新的虚拟网络，并将资源迁移到这些虚拟网络中。

有关更多信息：
* 通过完成 [Azure 虚拟网络教程](https://docs.azure.cn/zh-cn/virtual-network/#step-by-step-tutorials)来刷新您的知识。
* 查阅[虚拟网络概述](https://docs.azure.cn/zh-cn/virtual-network/virtual-networks-overview)。
* 了解如何[规划虚拟网络](https://docs.azure.cn/zh-cn/virtual-network/virtual-network-vnet-plan-design-arm)。
* 了解如何[创建 Vnet 对等互连](https://docs.azure.cn/zh-cn/virtual-network/virtual-network-peering-overview)。
* [如何使用不同的部署模型和订阅创建 Vnet 对等互连](https://docs.azure.cn/zh-cn/virtual-network/create-peering-different-deployment-models-subscriptions)。
* [如何使用不同的订阅创建 Vnet 对等互连](https://docs.azure.cn/zh-cn/virtual-network/create-peering-different-subscriptions)。

### 网络安全组

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

### ExpressRoute

目前不支持跨 Azure 区域迁移 Azure ExpressRoute 实例。对于跨云类型的迁移，我们建议您在目标 Azure 区域中创建新的 ExpressRoute 线路和新的 ExpressRoute 网关。

有关更多信息：
* 通过完成 [ExpressRoute 教程](https://docs.azure.cn/zh-cn/expressroute/#step-by-step-tutorials)来刷新您的知识。
* 了解如何[创建新的 ExpressRoute 网关](https://docs.azure.cn/zh-cn/expressroute/expressroute-howto-add-gateway-portal-resource-manager)。
* 了解 [ExpressRoute 位置和服务提供商](https://docs.azure.cn/zh-cn/expressroute/expressroute-locations)。
* 阅读有关 [ExpressRoute 的虚拟网络网关](https://docs.azure.cn/zh-cn/expressroute/expressroute-about-virtual-network-gateways)的内容。
 
### VPN 网关

目前不支持跨 Azure 区域迁移 Azure VPN 网关实例。我们建议您在新区域中创建和配置 VPN 网关的新实例。  
您可以使用门户网站或 PowerShell 收集有关当前 VPN 网关配置的信息。在 PowerShell 中，使用以 `Get-AbureRmVirtualNetworkGateway` 开头的一组 cmdlet。  
确保更新您的本地配置。此外，在更新 Azure 网络环境后，删除旧 IP 地址范围内的任何现有规则。

有关更多信息：
* 通过完成 [VPN 网关教程](https://docs.azure.cn/zh-cn/vpn-gateway/#step-by-step-tutorials)来刷新您的知识。
* 了解如何[创建站点到站点的连接](https://docs.azure.cn/zh-cn/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)。
* 查阅 [Get-AzureRmVirtualNetworkGateway PowerShell cmdlet](https://docs.microsoft.com/zh-cn/powershell/module/azurerm.network/get-azurermvirtualnetworkgateway?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.5.0)。
* 阅读博客文章：[创建站点到站点的连接](https://blogs.technet.microsoft.com/ralfwi/2017/02/02/connecting-clouds/)。

### 应用程序网关

目前不支持跨 Azure 区域迁移 Azure 应用程序网关实例。我们建议您在新区域中创建和配置新网关。  
您可以使用门户网站或 PowerShell 收集有关当前网关配置的信息。在 PowerShell 中，使用以 Get- AzureRmApplicationGateway 开头的一组 cmdlet。

有关更多信息：
* 通过完成[应用程序网关教程](https://docs.azure.cn/zh-cn/application-gateway/#step-by-step-tutorials)来刷新您的知识。
* 了解如何[创建应用程序网关](https://docs.azure.cn/zh-cn/application-gateway/quick-create-portal)。
* 查阅[应用程序网关 PowerShell cmdlet](https://docs.microsoft.com/en-us/powershell/module/azurerm.network/?view=azurermps-6.13.0#application_gateway)。

### DNS (To Be Checked)

要在 Azure 区域中迁移 Azure DNS 配置，请导出 DNS 区域文件，然后在新订阅下导入它。目前，导出区域文件的唯一方法是使用 Azure CLI。  
登录 Azure 区域中的源订阅后，将 Azure CLI 配置为使用 Azure 资源管理器模式。通过运行以下命令导出区域：
```
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```
示例:
```
az network dns zone export -g "myresourcegroup" -n "contoso.com" -f "contoso.com.txt"
```
此命令调用 Azure DNS 服务以导出资源组 myresourcegroup 中的区域 contoso.com。输出作为一个兼容 BIND 的区域文件存储在当前文件夹的 contoso.com.txt 文件中。
导出完成后，从区域文件中删除 NS 记录。为新区域和订阅创建新的 NS 记录。
接下来，登录到目标环境，创建新资源组（或选择现有资源组），然后导入区域文件：
```    
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```
导入区域后，您必须通过运行以下命令来验证区域：
```
az network dns record-set list -g <resource group> -z <zone name>
```
验证完成后，请与您的域名注册商联系并重新授权 NS 记录。要获取 NS 记录信息，请运行以下命令：
```
az network dns record-set ns list -g <resource group> -z --output json
```
有关更多信息：
* 通过完成 [Azure DNS 教程](https://docs.azure.cn/zh-cn/dns/#step-by-step-tutorials)来刷新您的知识。
* 查阅 [Azure DNS 概述](https://docs.azure.cn/zh-cn/dns/dns-overview)。
* 了解有关 [Azure DNS 导入和导出的更多信息](https://docs.azure.cn/zh-cn/dns/dns-import-export)。

### 网络观察程序

目前不支持跨 Azure 区域迁移网络观察程序实例。我们建议您在目标区域中创建和配置新网络观察程序。然后，比较旧环境和新环境之间的结果。
 
有关更多信息：
* 通过完成[网络观察程序教程](https://docs.azure.cn/zh-cn/network-watcher/#step-by-step-tutorials)来刷新您的知识。
* 查阅[网络观察程序概述](https://docs.azure.cn/zh-cn/network-watcher/network-watcher-monitoring-overview)。
* 了解有关[网络安全组流日志](https://docs.azure.cn/zh-cn/network-watcher/network-watcher-nsg-flow-logging-portal)的更多信息。
* 阅读有关[连接监视器](https://docs.azure.cn/zh-cn/network-watcher/connection-monitor)的内容。

### 流量管理器

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

### 负载均衡器

目前不支持跨 Azure 区域迁移负载均衡器实例。我们建议您在目标 Azure 区域中创建和配置新的负载均衡器。
如果您当前正在使用 [Azure负载均衡器 - **基础版**](https://docs.azure.cn/zh-cn/load-balancer/quickstart-create-basic-load-balancer-portal)，则建议您升级到 [Azure 负载均衡器 - **标准版**](https://docs.azure.cn/zh-cn/load-balancer/quickstart-load-balancer-standard-public-portal)。
了解有关[为何使用标准负载均衡器](https://docs.azure.cn/zh-cn/load-balancer/load-balancer-standard-overview#why-use-standard-load-balancer)的更多信息，包括[限制](https://docs.azure.cn/zh-cn/azure-subscription-service-limits#load-balancer)和[定价](https://www.azure.cn/zh-cn/pricing/details/load-balancer/)。

*注意：*
*由于我们继续为负载均衡器添加新的功能和特性，我们预计它们只能在标准 SKU 上使用。*

有关更多信息：
* 通过完成[负载均衡器教程](https://docs.azure.cn/zh-cn/load-balancer/quickstart-create-basic-load-balancer-portal)来刷新您的知识。
* 查阅[负载均衡器概述](https://docs.azure.cn/zh-cn/load-balancer/load-balancer-overview)。
* 了解如何[创建新的负载均衡器](https://docs.azure.cn/zh-cn/load-balancer/quickstart-load-balancer-standard-public-portal)。

## 迁移存储资源

本节包含的信息可以帮助您跨Azure区域迁移Azure存储资源。

### Blobs

AzCopy 是一个免费工具，可用于复制 blob、文件和表。使用 AzCopy 进行迁移，以便跨 Azure 区域复制 blob。
如果不对源 VM 使用托管磁盘，请使用 AzCopy 将 .vhd 文件复制到目标环境。如果使用托管磁盘，请参阅**托管磁盘**。
以下示例显示了 AzCopy 的工作原理。有关完整参考文献，请参阅[AzCopy文档](https://docs.azure.cn/zh-cn/storage/common/storage-use-azcopy-v10)。
AzCopy 使用术语 Source 和 Dest 来表示 URI。
 
通过使用 PowerShell 或使用 Azure CLI，您可从门户获得 URI 的三个部分（storageaccountname、containername、blobname）。Blob 的名称可以是 URI 的一部分，也可以作为一种模式提供，如vm121314.vhd。
您还需要使用 Azure Active Directory 或 SAS 令牌进行身份验证才能访问 Azure 存储帐户。有关如何进行身份验证的说明，请参阅[身份验证选项](https://docs.azure.cn/zh-cn/storage/common/storage-use-azcopy-v10#authentication-options)。

例如：  
<table>   
  <tr>      
    <td>URI part</td>      
    <td>ExampleValue</td>
  </tr>   
  <tr>      
    <td>Source storageAccount</td>   
    <td>migratetest</td>
  </tr>   
  <tr>      
    <td>Source container</td>   
    <td>vhds</td>
  </tr>
  <tr>
    <td>Source blob</td>
    <td>vm-121314.vhd</td>
  </tr>
  <tr>
    <td>Target storageAccount</td>
    <td>migratetarget</td>
  </tr>
  <tr>
    <td>Target container</td>
    <td>targetcontainer</td>
  </tr>
</table>

此命令跨 Azure 区域复制虚拟硬盘：
```
azcopy cp https://migratetest.blob.core.windows.net/vhds/vm-121314.vhd?<sastokenhere>
```
```
https://migratetarget.blob.core.windows.net/targetcontainer?<sastokenhere>
```
要获得 VHD 的一致副本，请在复制 VHD 之前关闭 VM。为复制操作规划一些停机时间。复制 VHD 后，[在目标环境中重建 VM](https://docs.azure.cn/zh-cn/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks)。

有关更多信息：
* 查阅 [AzCopy 文档](https://docs.microsoft.com/zh-cn/azure/storage/common/storage-use-azcopy-v10)。
* 了解如何[从还原的磁盘创建 VM](https://docs.azure.cn/zh-cn/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks)。

### 托管磁盘
Azure 托管磁盘通过管理与 VM 磁盘关联的存储帐户，简化了 Azure 基础结构即服务 (IaaS) VM 的磁盘管理。
由于您无法直接访问 .vhd 文件，因此无法直接使用 AzCopy 等工具来复制文件。解决方法是首先通过获取临时共享访问签名 URI 来导出托管磁盘，然后使用此信息下载或复制它。以下部分显示了如何获取共享访问签名 URI 以及如何处理它的示例：
 
#### 步骤 1：获取共享访问签名 URI
1. 在门户中，搜索托管磁盘。它与 VM 位于同一资源组中，其资源类型为**磁盘**。
2. 在**概述**页面上，选择顶部菜单中的**导出**按钮。您必须先关闭并取消分配 VM，或者取消附加 VM 以完成导出。
3. 定义 URI 到期的时间。默认时间为 3600 秒。
4. 生成一个 URL。
5. 复制该 URL。该 URL 只会在创建后显示一次。

#### 步骤 2：AzCopy
有关如何使用 AzCopy 的示例，请参阅下面的Blob。使用 AzCopy 或类似工具将磁盘直接从源环境复制到目标环境。
在 AzCopy 中，您必须将 URI 拆分为基础 URI 和共享访问签名部分。URI 的共享访问签名部分以字符 "?" 开头。
门户为共享访问签名 URI 提供此 URI：
```
https://md-kp4qvrzhj4j5.blob.core.windows.net/r0pmw4z3vk1g/abcd?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D>
```
以下命令显示了 AzCopy 的源参数：
```
/source:"https://md-kp4qvrzhj4j5.blob.core.windows.net/r0pmw4z3vk1g/abcd" 
```
```
/sourceSAS:"?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D"
```
这是完整的命令：
```
azcopy -v /source:"https://md-kp4qvrzhj4j5.blob.core.windows.net/r0pmw4z3vk1g/abcd" /sourceSAS:"?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D" /dest:"https://migratetarget.blob.core.windows.net/targetcontainer/newdisk.vh d" /DestKey:"o//ucD\... Kdpw=="
```
#### 步骤 3：在目标环境中创建新的托管磁盘
有几种方法可用于创建新的托管磁盘。以下是在 Azure 门户执行该操作的方法：
1. 在门户中，选择**新建 > 托管磁盘 > 创建**。
2. 输入新磁盘的名称。
3. 选择资源组。
4. 在**源类型**下，选择**存储 blob**。然后，从 AzCopy 命令复制目标 URI，或进行浏览以选择目标 URI。
5. 如果复制了 OS 磁盘，请选择**OS**类型。对于其他磁盘类型，请选择**创建**。

#### 步骤 4：创建 VM
如前所述，有多种方法可以使用此新托管磁盘创建 VM。以下是两种选项：
* 在门户中，选择磁盘，然后选择**创建 VM**。像往常一样定义 VM 的其他参数。
* 有关 PowerShell 的更多信息，请参阅[从还原的磁盘创建 VM](https://docs.azure.cn/zh-cn/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks)。

有关更多信息：
* 通过获取共享访问签名 URI，了解如何[通过 API](https://docs.microsoft.com/zh-cn/rest/api/compute/disks/grantaccess) 导出到磁盘。
* 了解如何[通过 API](https://docs.microsoft.com/zh-cn/rest/api/compute/disks/createorupdate#create_a_managed_disk_by_importing_an_unmanaged_blob_from_a_different_subscription.) 从非托管 blob 创建托管磁盘。

### 导入/导出

您无法跨 Azure 区域直接迁移 Azure 导入/导出作业资源。Azure 导入/导出服务不支持资源导出或资源导入。
在区域中创建的 Azure 导入/导出作业资源需要在该区域中完成，以便将数据提取到该区域中的存储帐户或从该区域中的存储帐户导出。
但是，您可以使用新区域中的存储帐户，在新区域中创建新的 Azure 导入/导出作业资源。
也可以通过将 Azure 导入/导出资源导出为[资源管理器模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)创建新的作业资源，然后调整目标 Azure 区域的导出模板以重新创建资源。

*注意:
导出 Azure 导入/导出模板不会复制数据（例如，在存储帐户中创建的 Blob）。导出模板仅重新创建 Azure 导入/导出元数据。
请考虑更改适用于新区域的交付包、送货信息、存储帐户 ID 和其他作业属性。 

#### Azure 导入/导出元数据
导出 Azure 导入/导出模板时，将重新创建以下元数据元素：
* 作业资源

有关更多信息：
* 查阅 [Azure 导入/导出服务概述](https://docs.azure.cn/zh-cn/storage/common/storage-import-export-service)。
* Azure 导入/导出[常见问题](https://docs.azure.cn/zh-cn/storage/common/storage-import-export-service-faq)。
* 熟悉如何[导出 Azure 资源管理器模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)，或阅读 [Azure 资源管理器概述](https://docs.azure.cn/zh-cn/azure-resource-manager/resource-group-overview)。

### StorSimple

目前不支持将 StorSimple 服务从一个 Azure 区域迁移到另一个 Azure 区域。我们建议您按照[此处](https://docs.microsoft.com/zh-cn/azure/storsimple/storsimple-8000-migrate-classic-azure-portal#datacenter-changes)描述的手动过程操作或联系客户支持。

## 迁移 Web 资源

本节包含的信息可帮助您跨 Azure 区域迁移 Azure Web 资源。

### Web 应用

目前不支持跨 Azure 区域迁移使用 Azure App Service 的 Web 应用功能
创建的应用。我们建议您将 Web 应用导出为资源管理器模板，备份 Web 应用的文件内容（或确保应用的源代码在源代码控制存储库外部提供），并确保您已脱机存储任何与 Web 应用一同使用的自定义 SSL 证书。然后在将资源管理器模板中的位置属性更改为新区域后重新创建 Web 应用。在新区域中重新创建 Web 应用后，重新发布 Web 应用的文件内容，并上传并重新绑定 Web 应用使用的任何自定义 SSL 证书。

*重要事项:*  
*更改位置、Azure 密钥保管库密码、证书和其他 GUID 以与新区域保持一致。*

有关更多信息：
* 通过完成应用服务教程来刷新您的知识。
* 获取有关如何导出 Azure 资源管理器模板的信息。
* 查阅 Azure 资源管理器概述。
* 查阅应用服务概述。
* 获取 Azure 位置概述。
* 了解如何重新部署模板。

### API 管理

要将 API 管理端点从一个 Azure 区域迁移到另一个 Azure 区域，可以使用备份和还原功能。您应该在源和目标区域中选择相同的 API 管理 SKU。

注意:
在不同云类型之间迁移时，备份和还原将不起作用。为此，您需要将资源导出为模板。然后，针对目标 Azure 区域调整导出的模板并重新创建资源。

选项 1：如果您可以接受其他 API 管理实例的名称，请遵循这些说明：
1. 使用与新名称目标区域中的源 API 管理实例相同的 SKU 创建新的 API 管理实例。
2. 将现有 API 管理实例备份到存储帐户。
3. 在目标区域中，将步骤 2 中创建的备份还原为步骤 1 中创建的 API 管理实例。
4. 如果您有一个指向源区域 API 管理实例的自定义域，请更新自定义域 CNAME 以指向新的 API 管理实例。

选项 2：如果要保留 API 管理实例名称，请按照以下说明操作

注意:
这是一个风险更高的选择，将导致服务停机。
1. 将源区域中的 API 管理实例备份到一个存储帐户。
2. 删除源区域中的 API 管理实例。
3. 使用与源区域中相同的名称，在目标区域中创建一个新的 API 管理实例。
4. 在目标区域中，将步骤 1 中创建的备份还原为新的 API 管理实例。

## 迁移数据库资源

本节包含的信息可帮助您跨 Azure 区域迁移 Azure 数据库资源。

### SQL 数据库

要迁移 Azure SQL 数据库工作负载，请使用异地复制。有关详细说明，请参阅博客文章将 Azure 服务迁移到新区域。

注意:
导出操作后连接字符串会更改，因为导出期间服务器的 DNS 名称会更改。

有关更多信息：
* 了解如何将数据库导出到 BACPAC 文件。
* 了解如何将 BACPAC 文件导入数据库。
* 查阅 Azure SQL 数据库文档。

### 适用于 MySQL 和 PostgreSQL 的 Azure 数据库

如果已为服务器配置地域冗余备份，则可以将服务器还原到服务可用的另一个 Azure 区域。当您的服务器因服务器托管区域中的事件而不可用时，地域还原是默认的恢复选项。如果某个区域中的大规模事件导致数据库应用程序不可用，则可以将服务器从地域冗余备份还原到任何其他区域中的服务器。在进行备份和将其复制到不同区域之间存在延迟。此延迟可能长达一个小时，因此，如果发生灾难，最多可能会丢失一小时的数据。
在地域还原期间，可以更改的服务器配置包括计算代系、vCore、备份保留期和备份冗余选项。不支持更改定价层级（基础版、通用版或内存优化版）或存储大小。

有关更多信息：
* 了解如何备份和还原 Azure Database for MySQL
* 了解如何备份和还原 Azure Database for PostgreSQL

### SQL Server Stretch Database

目前不支持跨 Azure 区域迁移 SQL Server Stretch Database 。

### SQL 数据仓库

目前不支持跨 Azure 区域迁移 SQL 数据仓库。

### Azure Cache for Redis

如果要跨 Azure 区域迁移 Azure Cache for Redis 实例，可以有几个选项。您可以根据自身需要进行选择。

选项 1：接受数据丢失，创建一个新实例
当满足以下两个条件时，此方法最合理：
* 您正在使用 Azure Cache for Redis 作为临时数据缓存。
* 您的应用程序将在新区域中自动重新填充缓存数据。

要在数据丢失的情况下迁移并创建新实例：
1. 在新目标区域中创建新的 Azure Cache for Redis 实例。
2. 更新您的应用程序以使用新区域中的新实例。
3. 删除源区域中的旧 Azure Cache for Redis 实例。

选项 2：将数据从源实例复制到目标实例
Azure Cache for Redis 团队的一名成员编写了一个开源工具，可以将数据从一个 Azure Cache for Redis 实例复制到另一个实例，而无需使用导入或导出功能。有关该工具的信息，请参阅以下步骤中的步骤 4。
要将数据从源实例复制到目标实例：
1. 在源区域中创建 VM。如果 Azure Cache for Redis 中的数据集很大，请确保选择相对较大的 VM 大小以尽量减少复制时间。
2. 在新目标区域中创建新的 Azure Cache for Redis。
3. 从目标实例刷新数据。（确保不要从源实例刷新。由于复制工具不会覆盖目标位置中的现有密钥，因此需要刷新。）
4. 使用以下工具自动将源 Azure Cache for Redis 实例中的数据复制到目标 Azure Cache for Redis 实例：工具源代码和工具下载。

*注意:*  
*此过程可能需要很长时间，具体取决于数据集的大小。*

选项 3：从源实例导出并导入到目标实例
此方法利用仅在 Premium 层中提供的功能。要从源实例导出并导入到目标实例：
1. 在目标区域中创建新的 Premium 层 Azure Cache for Redis。使用与源 Azure Cache for Redis 实例相同的大小。
2. 从源缓存导出数据或使用 Export-AzureRmRedisCache PowerShell cmdlet。

*注意:*  
*导出 Azure 存储帐户必须与缓存实例位于同一区域。*

3. 使用 AzCopy 之类的工具将导出的 blob 复制到目标区域中的存储帐户。
4. 将数据导入目标缓存或使用 Import-AzureRmRedisCache PowerShell cmdlet。
5. 重新配置应用程序以使用目标 Azure Cache for Redis 实例。

选项 4：将数据写入两个 Azure Cache for Redis 实例，并从一个实例中读取
对于此方法，您必须修改应用程序。从其中一个缓存实例读取数据时，应用程序需要将数据写入多个缓存实例。如果存储在 Azure Cache for Redis 中的数据满足以下条件，则可采用此方法：
* 数据会定期刷新。
* 所有数据都将写入目标 Azure Cache for Redis 实例。
* 您有足够的时间刷新所有数据。
 
有关更多信息：
* 查阅 Azure Cache for Redis 概述。

## 迁移容器资源

本节包含的信息可帮助您跨 Azure 区域迁移 Azure 容器资源。

### Azure 容器注册表

要跨 Azure 区域迁移 Azure 容器注册表，请使用异地复制。但是，当您必须跨云类型迁移 Azure 容器注册表时，异地复制不起作用。如果要跨云类型移动 Azure 容器注册表实例，请在目标区域中创建新的容器注册表，并使用导入 API 将容器映像导入到创建的新注册表中。

## 迁移分析资源

本节提供的信息可帮助您跨 Azure 区域迁移 Azure 分析资源。

### HDInsight

要跨区域迁移 HDInsight 服务，可以将 HDInsight 资源导出为资源管理器模板，然后针对目标 Azure 区域调整导出的模板并重新创建资源。

*注意：*  
*导出 HDInsight 模板不会复制数据（例如，临时数据）。导出模板仅重新创建 HDInsight 元数据。*


要跨 Azure 区域迁移 Azure HDInsight 群集：
1. 停止 HDInsight 群集。
2. 使用 AzCopy 或类似工具将 Azure 存储帐户中的数据迁移到新区域。
3. 在目标 Azure 区域中创建新的 HDInsight 资源，然后将迁移的存储资源作为主要连接存储附加。
对于更加特殊化、长期运行的群集（Kafka、Spark 流、Storm 或 HBase），我们建议您将工作负载转换到新区域。

有关更多信息：
* 查阅 Azure HDInsight 文档。
* 通过完成 HDInsight 教程来刷新您的知识。
* 有关扩展 HDInsight 群集的帮助，请参阅使用 PowerShell 管理 HDInsight。
* 了解如何使用 AzCopy。

### 事件中心

您无法跨 Azure 区域直接迁移 Azure 事件中心资源。事件中心服务没有数据导出或导入功能。您可以将事件中心资源导出为资源管理器模板，然后针对目标 Azure 区域调整导出的模板并重新创建资源。

*注意:*  
*导出事件中心模板不会复制数据（例如，消息）。导出模板仅重新创建事件中心元数据。*

*重要事项：*  
*更改位置、Azure 密钥保管库密码、证书和其他 GUID 以与新区域保持一致。*

#### 事件中心元数据

导出事件中心模板时，将重新创建以下元数据元素：
* 命名空间
* 事件中心
* 使用者组
* 授权规则

有关更多信息：
* 查阅事件中心概述。
* 通过完成事件中心教程来刷新您的知识。
* 检查Azure 服务总线的迁移步骤。
* 熟悉如何导出 Azure 资源管理器模板，或阅读 Azure 资源管理器的概述。
 
### 流分析

要跨 Azure 区域迁移 Azure 流分析服务，最简单的方法是使用适用于 Azure 流分析的 Visual Studio 工具将作业复制到其他区域。查阅此博客文章中以了解更多信息。
您还可以使用 Azure 门户或使用 PowerShell 在目标 Azure 区域中手动重新创建整个设置。流分析作业的入口和出口源可以位于任何区域。

有关更多信息：
* 通过完成流分析教程来刷新您的知识。
* 查阅流分析概述。
* 了解如何使用 PowerShell 创建流分析作业。

### Analysis Services

要跨 Azure 区域迁移 Azure Analysis Services 模型，请使用备份和还原操作。
如果只想迁移模型元数据而不是数据，则可以选择从 SQL Server 数据工具重新部署模型。
 
有关更多信息：
* 了解 Analysis Services 备份和还原。
* 查阅分析服务概述。

### PowerBI

目前不支持跨 Azure 区域迁移 PowerBI，但您可以使用 Power BI Premium 将选定的工作区迁移到另一个 Azure 区域。有关更多信息，请参阅为 Power BI Premium 配置多地理位置支持。

## 将 IoT 资源迁移到目标 Azure 区域

本节包含的信息可帮助您跨 Azure 区域迁移 Azure IoT 资源。
 
### Functions

目前不支持跨 Azure 区域迁移 Azure Functions 资源。我们建议您导出资源管理器模板，更改位置，然后重新部署到目标区域。

*重要事项：*  
*更改位置、Azure 密钥保管库密码、证书和其他 GUID 以与新区域保持一致。*

有关更多信息：
* 通过完成 Functions 教程来刷新您的知识。
* 了解如何导出资源管理器模板或阅读 Azure 资源管理器概述。
* 查阅 Azure 功能概述。
* 阅读 Azure 位置概述。
* 了解如何重新部署模板。

### 通知中心

要将设置从一个 Azure 通知中心实例迁移到另一个实例，请导出然后导入所有注册令牌和标记：
1. 将现有通知中心注册导出到 Azure Blob 存储容器。
2. 在目标环境中创建新的通知中心。
3. 将注册令牌从 Blob 存储导入新的通知中心。

有关更多信息：
* 通过完成通知中心教程来刷新您的知识。
* 查阅通知中心概述。

### IoT 中心

要迁移 IoT 中心，请重新创建 IoT 中心并使用导出/导入设备标识功能：

*注意：*  
*此迁移可能会导致 Azure IoT 应用程序出现停机和数据丢失。所有遥测消息、C2D 命令和与作业相关的信息（计划表和历史记录）都不会迁移。您必须重新配置设备和后端应用程序才能开始使用新的连接字符串。*

步骤 1：重新创建 IoT 中心
IoT 中心不支持本机克隆。但是，您可以使用 Azure 资源管理器功能将资源组导出为模板以导出 IoT 中心元数据。配置的路由和其他 IoT 中心设置包含在导出的元数据中。然后，在全局 Azure 中重新部署模板。通过查看导出的 JSON 中的详细信息，您可以更轻松地在 Azure 门户中重新创建 IoT 中心。

步骤 2：迁移设备标识
要迁移设备标识：
1. 在源租户中，使用 ExportDevices资源管理器将所有设备标识、设备孪生和模块孪生（包括密钥）导出到存储容器。您可以在源 Azure 区域或目标 Azure 区域中使用存储容器。确保生成的共享访问签名 URI 具有足够的权限。
2. 运行 ImportDevices 资源管理器 API，将所有设备标识从存储容器导入到目标 Azure 区域中的克隆 IoT 中心。
3. 重新配置设备和后端服务，以开始使用步骤 1 中创建的新 IoT 中心的新连接字符串。

*注意：*
*如果要跨云类型迁移资源，则源和目标区域中的根证书颁发机构可能会有所不同。重新配置与 IoT 中心实例交互的设备和后端应用程序时，请考虑此问题。*

有关更多信息：
* 了解如何导出 IoT 中心批处理标识。
* 了解如何导入 IoT 中心批处理标识。
* 查阅 Azure IoT 中心概述。
 
## 将集成资源迁移到目标 Azure 区域

本节包含的信息可帮助您跨 Azure 区域迁移 Azure 集成资源。

### 服务总线

Azure 服务总线服务没有数据导出或导入功能。要跨 Azure 区域迁移服务总线资源，可以将资源导出为[Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal%23export-resource-groups-to-templates)。然后，针对目标 Azure 区域调整导出的模板并重新创建资源。

*注意:*  
*导出资源管理器模板不会复制数据（例如，消息）。导出模板仅重新创建元数据。*

*重要事项：*  
*更改位置、Azure 密钥保管库密码、证书和其他 GUID 以与新区域保持一致。*

#### 服务总线元数据

导出资源管理器模板时，将重新创建以下服务总线元数据元素：
* 命名空间
* 队列
* 主题
* 订阅
* 规则
* 授权规则

### 密钥

上述导出和重新创建的步骤不会复制与授权规则相关联的共享访问签名密钥。如果需要保留共享访问签名密钥，请使用带有可选参数 -Keyvalue 的 New-AzureRmServiceBuskey cmdlet将密钥作为字符串接受。更新的 cmdlet 可在 PowerShell Gallery release 6.4.0（2018 年 7 月）或 GitHub 上找到。 

用法示例:
```PowerShell
New-AzureRmServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string-keyvalue>
New-AzureRmServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Queue <queuename> -Name <name of Authorization rule> - RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string-keyvalue>
New-AzureRmServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Topic <topicname> -Name <name of Authorization rule> - RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string-keyvalue>
```
*注意:*  
*即使保存了密钥，也必须更新应用程序以使用新的连接字符串。*

有关更多信息：
* 通过完成服务总线教程来刷新您的知识。
* 熟悉如何导出资源管理器模板，或阅读 Azure 资源管理器的概述。
* 查阅服务总线概述。

### 逻辑应用

Azure 计划程序[将于 2019 年 9 月 30 日停用](https://azure.microsoft.com/updates/azure-scheduler-will-retire-on-september-30-2019/)。使用逻辑应用在目标 Azure 区域中创建计划作业。有关从计划程序迁移到逻辑应用的详细步骤，请参阅将 Azure 计划程序作业迁移到 Azure 逻辑应用。

有关更多信息：
* 通过完成逻辑应用教程，熟悉 Azure 逻辑应用中的功能。
* 查阅 Azure 逻辑应用概述。

## 迁移身份资源

本节包含的信息可帮助您跨 Azure 区域迁移 Azure 身份资源。

### 多重身份验证

您需要在新环境中重新创建用户并重新定义 Azure 多重身份验证实例。
 
要获取启用或强制执行多重身份验证的用户帐户列表：
1. 登录 Azure 门户。
2. 选择用户 > 所有用户 > 多重身份验证。
3. 当您被重定向到多重身份验证服务页面时，请设置适当的筛选器以获取用户列表。

有关更多信息：
* 了解有关 Azure 多重身份验证的更多信息。

## 迁移安全资源

本文提供的信息可帮助您跨 Azure 区域迁移 Azure 安全资源。

### 密钥保管库

Azure 密钥保管库的某些功能无法跨 Azure 区域迁移。

#### 加密密钥

您无法迁移加密密钥。在目标区域中创建新密钥，然后使用该密钥保护目标资源（例如，Azure 存储或 Azure SQL 数据库）。安全地将数据从旧区域迁移到新区域。

#### 应用程序密码

应用程序密码是证书、存储帐户密钥和其他与应用程序相关的密码。在迁移期间，首先在目标 Azure 区域中创建新的密钥保管库。然后，完成以下操作之一：
* 创建新的应用程序密码。
* 读取源 Azure 区域中的当前密码，然后在新保管库中输入值。
```PowerShell
Get-AzureKeyVaultSecret -vaultname mysecrets -name Deploydefaultpw
```
有关更多信息：
* 通过完成密钥保管库教程来刷新您的知识。
* 查阅密钥保管库概述。
* 查阅密钥保管库 PowerShell cmdlet。

## 迁移管理工具资源

本文提供的信息可帮助您跨 Azure 区域迁移 Azure 管理工具。

### 备份

Azure 备份是基于 Azure 的服务，用于备份（或保护）和还原 Azure 中的数据。该服务可以保护本机 Azure 资源（如 VM）以及来自混合环境的非 Azure 资源（包括在本地服务器上运行的工作负载）。所有备份都存储在 Azure 的恢复服务保管库中。
如果客户需要在 Azure 区域之间迁移，则应首先在目标区域中创建可以保护新区域中资源的新恢复服务保管库。要迁移受 Azure 备份保护的 Azure 资源，客户首先需要停止对资源的保护并保留现有数据（说明）。然后，可以将资源迁移到目标区域，并且可以在新创建的恢复保管库中启用保护。对于非 Azure 资源，客户将遵循相同的过程，但不需要迁移资源。仍可访问源区域中的现有备份数据以支持恢复场景。
对于希望统一备份的客户，Azure 备份正在尝试提供一种工具，以便将现有数据移动到目标区域，同时确保客户仍可访问现有恢复点。该工具预计将在 CY20H1 中提供。

有关更多信息：
* 通过完成 备份教程来刷新您的知识。
* 查阅 Azure 备份概述。

### 计划程序

Azure 计划程序[将于 2019 年 9 月 30 日停用](https://azure.microsoft.com/updates/azure-scheduler-will-retire-on-september-30-2019/)。使用 Azure 逻辑应用创建计划作业。有关从计划程序迁移到逻辑应用的详细步骤，请参阅将 Azure 计划程序作业迁移到 Azure 逻辑应用。

有关更多信息：
* 通过完成逻辑应用教程，熟悉 Azure 逻辑应用中的功能。
* 查阅逻辑应用概述。
 
### 站点恢复

您无法跨 Azure 区域移动现有的 Azure 站点恢复设置。禁用现有配置并在目标 Azure 区域中设置新的站点恢复解决方案。
通过完成这些分步教程来刷新您的知识：
* 从 Azure 到 Azure 的灾难恢复
* 从 Vmware 到 Azure 的灾难恢复
* 从 Hyper-V 到 Azure 的灾难恢复

## 迁移媒体资源

本节包含的信息可帮助您跨 Azure 区域迁移 Azure 媒体资源。

### 媒体服务

在 Azure 媒体服务中，您可以配置自己的存储帐户和所有媒体资产。首先，在目标 Azure 区域中创建新的媒体服务帐户。然后，重新加载相应的媒体项目，并在新的媒体服务帐户下完成编码和流式传输。
 
有关更多信息：
* 通过完成媒体服务教程来刷新您的知识。
* 查阅媒体服务概述。
* 了解如何创建媒体服务帐户。

### 媒体播放器

您可以在 Azure 媒体播放器中选择多个端点。您可以将内容从源 Azure 端点流式传输到目标 Azure 端点。
有关更多信息，请参阅 Azure 媒体播放器。
