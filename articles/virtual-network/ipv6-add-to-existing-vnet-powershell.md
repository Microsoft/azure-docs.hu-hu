---
title: IPv4-alkalmazás frissítése az IPv6-ra az Azure Virtual Network-PowerShellben
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan telepíthet IPv6-címeket egy meglévő alkalmazásba az Azure Virtual Network Azure PowerShell használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9d2ff583b032ff1f9aa5dbc9706ea6c981fc7265
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009976"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>IPv4-alkalmazás frissítése az IPv6-ra az Azure Virtual Networkben – PowerShell

Ez a cikk bemutatja, hogyan adhat IPv6-kapcsolatot egy meglévő IPv4-alkalmazáshoz egy standard Load Balancer és egy nyilvános IP-címmel rendelkező Azure-beli virtuális hálózaton. A helyben történő frissítés a következőket tartalmazza:
- IPv6-címtartomány a virtuális hálózathoz és az alhálózathoz
- IPv4-és IPV6-alapú előtér-konfigurációval rendelkező standard Load Balancer
- Az IPv4 + IPv6-konfigurációt is tartalmazó hálózati adapterekkel rendelkező virtuális gépek
- IPv6 nyilvános IP-cím, hogy a terheléselosztó internetkapcsolattal rendelkező IPv6-kapcsolattal rendelkezik

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez a Azure PowerShell modul 6.9.0 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy üzembe helyezett egy standard Load Balancer a következő témakörben leírtak szerint [: standard Load Balancer-Azure PowerShell létrehozása](../load-balancer/quickstart-load-balancer-standard-public-powershell.md).

## <a name="retrieve-the-resource-group"></a>Az erőforráscsoport beolvasása

A kettős veremből származó virtuális hálózat létrehozása előtt le kell kérnie az erőforráscsoportot a [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)használatával.

```azurepowershell-interactive
$rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>IPv6 IP-címek létrehozása

Hozzon létre egy nyilvános IPv6-címeket a standard Load Balancer [új AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) . Az alábbi példa egy *PublicIP_v6* nevű IPv6 nyilvános IP-címet hoz létre a *myresourcegroupslb erőforráscsoportban* erőforráscsoporthoz:

```azurepowershell-interactive  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>A Load Balancer felületének konfigurálása

Kérje le a meglévő terheléselosztó-konfigurációt, majd adja hozzá az új IPv6 IP-címet az [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) használatával az alábbiak szerint:

```azurepowershell-interactive
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"

# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>A terheléselosztó háttér-készletének konfigurálása

Hozza létre a háttér-készletet a terheléselosztó konfigurációjának helyi példányán, és frissítse a futó Load balancert az új háttér-készlet konfigurációjának megfelelően a következőképpen:

```azurepowershell-interactive
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"

# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Terheléselosztási szabályok konfigurálása
Kérje le a meglévő Load Balancer előtér-és háttér-készlet konfigurációját, majd adja hozzá az új terheléselosztási szabályokat az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig)használatával.

```azurepowershell-interactive
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb

# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>IPv6-címtartományok hozzáadása

Adja hozzá az IPv6-címtartományt a virtuális hálózathoz és a virtuális gépeket üzemeltető alhálózathoz az alábbiak szerint:

```azurepowershell-interactive
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 

#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("fd00:db8:deca::/48")

#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]

#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("fd00:db8:deca::/64")

#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork
```

## <a name="add-ipv6-configuration-to-nic"></a>IPv6-konfiguráció hozzáadása a hálózati adapterhez

Konfigurálja az összes virtuálisgép-hálózati adaptert IPv6-cím használatával az [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) segítségével a következőképpen:

```azurepowershell-interactive
#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName

#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>IPv6-alapú kettős verem virtuális hálózatának megtekintése Azure Portal

Az IPv6 kettős verem virtuális hálózatát a következőképpen tekintheti meg Azure Portalban:
1. A portál keresési sávján adja meg a *myVnet*.
2. Ha a **myVnet** megjelenik a keresési eredmények között, válassza ki. Ez elindítja a *myVNet* nevű kettős verem virtuális hálózat **Áttekintés** lapját. A kettős verem virtuális hálózata a három hálózati adaptert jeleníti meg, amelyek IPv4-és IPv6-konfigurációval rendelkeznek, amelyek a *mySubnet* nevű kettős verem alhálózatában találhatók.

  ![IPv6-alapú kettős verem virtuális hálózata az Azure-ban](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás eltávolításához.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben frissített egy meglévő standard Load Balancer IPv4-es előtér-IP-konfigurációval egy kettős verem (IPv4 és IPv6) konfigurációra. Az IPv6-konfigurációkat a háttér-készletben található virtuális gépek hálózati adapterei és az azokat üzemeltető Virtual Network is felvette. További információ az Azure-beli virtuális hálózatok IPv6-támogatásáról: [Mi az IPv6 for azure Virtual Network?](ipv6-overview.md)
