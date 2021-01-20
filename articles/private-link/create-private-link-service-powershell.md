---
title: 'Rövid útmutató: Azure Private link Service létrehozása Azure PowerShell használatával'
description: Ismerje meg, hogyan hozhat létre Azure Private link Service-t a Azure PowerShell használatával
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 01/20/2021
ms.author: allensu
ms.openlocfilehash: 66ad5aae9f8175d154bb07a8b112dada175a205a
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610063"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Privát kapcsolati szolgáltatás létrehozása Azure PowerShell használatával

Ismerkedjen meg a szolgáltatásra hivatkozó privát link szolgáltatás létrehozásával.  Adja meg az Azure-standard Load Balancer mögött üzembe helyezett szolgáltatáshoz vagy erőforráshoz való magánhálózati hivatkozást.  A szolgáltatás felhasználói a virtuális hálózatról privát hozzáféréssel rendelkeznek.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Helyileg telepített Azure PowerShell vagy Azure Cloud Shell

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivLinkService-rg' -Location 'eastus2'

```
---
## <a name="create-an-internal-load-balancer"></a>Hozzon létre egy belső terheléselosztót

Ebben a szakaszban egy virtuális hálózatot és egy belső Azure Load Balancer hoz létre.

### <a name="virtual-network"></a>Virtuális hálózat

Ebben a szakaszban egy virtuális hálózatot és alhálózatot hoz létre a privát kapcsolati szolgáltatáshoz hozzáférő terheléselosztó üzemeltetéséhez.

* Hozzon létre egy új virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    PrivateLinkServiceNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig
}
$vnet = New-AzVirtualNetwork @net

```

### <a name="create-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

* Hozzon létre egy előtér-IP-címet a [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) for the frontend IP-készlethez. Ez az IP-cím fogadja a terheléselosztó bejövő forgalmát

* Hozzon létre egy háttér-címkészletet [új AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) a terheléselosztó felületéről továbbított forgalomhoz. Ez a készlet a háttérbeli virtuális gépek üzembe helyezésének helyét adja meg.

* Hozzon létre egy olyan [AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) , amely meghatározza a háttérbeli virtuálisgép-példányok állapotát.

* Hozzon létre egy terheléselosztó-szabályt az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) használatával, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

* Hozzon létre egy nyilvános Load balancert a [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).


```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus2'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-a-private-link-service"></a>Privát kapcsolati szolgáltatás létrehozása

Ebben a szakaszban hozzon létre egy privát hivatkozás-szolgáltatást, amely az előző lépésben létrehozott szabványos Azure Load Balancer használja.

* Hozza létre a Private link Service IP-konfigurációját a [New-AzPrivateLinkServiceIpConfig](/powershell/module/az.network/new-azprivatelinkserviceipconfig).

* Hozza létre a Private link szolgáltatást a [New-AzPrivateLinkService](/powershell/module/az.network/new-azprivatelinkservice).

```azurepowershell
## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create the IP configuration for the private link service. ##
$ipsettings = @{
    Name = 'myIPconfig'
    PrivateIpAddress = '10.1.0.5'
    Subnet = $vnet.subnets[0]
}
$ipconfig = New-AzPrivateLinkServiceIpConfig @ipsettings

## Place the load balancer frontend configuration into a variable. ##
$fe = Get-AzLoadBalancer -Name 'myLoadBalancer' | Get-AzLoadBalancerFrontendIpConfig

## Create the private link service for the load balancer. ##
$privlinksettings = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    LoadBalancerFrontendIpConfiguration = $fe
    IpConfiguration = $ipconfig
}
New-AzPrivateLinkService @privlinksettings
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és a többi erőforrás.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePrivLinkService-rg'
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőket hajtja végre:

* Létrehozott egy virtuális hálózatot és belső Azure Load Balancer.
* Privát hivatkozás szolgáltatás létrehozva

Ha többet szeretne megtudni az Azure Private-végpontról, folytassa a következővel:
> [!div class="nextstepaction"]
> [Rövid útmutató: privát végpont létrehozása az Azure PowerShell-lel](create-private-endpoint-powershell.md)

