---
title: IPv6-alapú Dual stack-alkalmazás üzembe helyezése szabványos belső Load Balancer használatával az Azure-ban – PowerShell
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan helyezhet üzembe egy IPv6-alapú Dual stack-alkalmazást az Azure Virtual Network standard belső Load Balancer az Azure PowerShell használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: 3df89b84e748f041f13866c1eb3c0b8a3341209c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98220830"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>IPv6-alapú kettős stack-alkalmazás üzembe helyezése standard belső Load Balancer az Azure-ban – PowerShell (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy Dual stack (IPv4 + IPv6) alkalmazást az Azure-ban, amely egy kettős veremből álló virtuális hálózatot és alhálózatot tartalmaz, egy standard belső Load Balancer kettős (IPv4 + IPv6) előtér-konfigurációval, valamint két IP-konfigurációval, hálózati biztonsági csoporttal és nyilvános IP-címmel rendelkező virtuális gépekkel.

Az IPv6-kompatibilis belső Load Balancer létrehozásának eljárása majdnem azonos az internetre irányuló, az [itt](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)ismertetett ipv6-Load Balancer létrehozási folyamatával. A belső terheléselosztó létrehozásának egyetlen eltérése az előtér-konfigurációban látható, ahogyan az alábbi PowerShell-példa is mutatja:

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "fd00:db8:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

A belső terheléselosztó előtér-konfigurációját használó módosítások a következők:
- Az `PrivateIpAddressVersion` "IPv6"-ként van megadva
- Az `-PublicIpAddress` argumentum el lett hagyva vagy le lett cserélve `-PrivateIpAddress` . Vegye figyelembe, hogy a magánhálózati címnek azon alhálózati IP-terület tartományba kell esnie, amelyben a belső terheléselosztó üzembe lesz helyezve. Ha a statikus `-PrivateIpAddress` érték nincs megadva, a következő ingyenes IPv6-cím lesz kiválasztva abban az alhálózatban, amelyben a belső terheléselosztó telepítve van.
- A kettős verem alhálózata, amelyben a belső terheléselosztó telepítve lesz, `-Subnet` vagy egy argumentummal van megadva `-SubnetId` .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez a Azure PowerShell modul 6.9.0 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="prerequisites"></a>Előfeltételek
A Dual stack-alkalmazás Azure-ban való üzembe helyezése előtt az alábbi Azure PowerShell használatával kell konfigurálnia az előfizetését ehhez az előzetes verzióhoz:

Regisztráljon a következőképpen:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
A szolgáltatás regisztrációjának befejezéséhez akár 30 percet is igénybe vehet. A regisztráció állapotát a következő Azure PowerShell parancs futtatásával tekintheti meg: a regisztrációt a következőképpen tekintheti meg:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
A regisztráció befejeződése után futtassa a következő parancsot:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

A kettős veremből álló virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával. A következő példában létrehozunk egy *dsStd_ILB_RG* nevű erőforráscsoportot az *USA keleti* régiójában:

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>IPv4-és IPv6-alapú nyilvános IP-címek létrehozása
A virtuális gépek internetről való eléréséhez IPv4-és IPv6-alapú nyilvános IP-címekre van szükség a virtuális gép számára. Hozzon létre nyilvános IP [-címeket a New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Az alábbi példa egy *RdpPublicIP_1* nevű IPv4-és IPv6-alapú nyilvános IP-címet hoz létre, és *RdpPublicIP_2* a *dsStd_ILB_RG* erőforráscsoporthoz:

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>A virtuális hálózat és az alhálózat létrehozása

Hozzon létre egy új virtuális hálózatot a New- [AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) és a Dual stack alhálózati konfiguráció használatával a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)használatával. A következő példában létrehozunk egy *dsVnet* nevű virtuális hálózatot a *dsSubnet*.

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","fd00:db8:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","fd00:db8:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ebben a szakaszban két előtérbeli IP-címet (IPv4 és IPv6) és a terheléselosztó háttér-címkészletet konfigurálja, majd létrehoz egy standard Load Balancer.

### <a name="create-front-end-ip"></a>Előtér-IP-cím létrehozása

Hozzon létre egy előtér-IP-címet a [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Az alábbi példa a *dsLbFrontEnd_v4* és *dsLbFrontEnd_v6* nevű IPv4-és IPv6-előtérbeli IP-konfigurációkat hozza létre:

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "fd00:db8:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>Háttérbeli címkészlet konfigurálása

Hozzon létre egy háttér-címkészletet a [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). A virtuális gépek ehhez a háttér-készlethez csatlakoznak a hátralévő lépésekben. Az alábbi példa létrehoz egy *dsLbBackEndPool_v4* nevű háttér-címkészletet, és *dsLbBackEndPool_v6* , hogy az IPv4-és IPv6-alapú NIC-konfigurációval rendelkező virtuális gépeket is tartalmazza:

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Annak érdekében, hogy csak az egészséges virtuális gépek kapnak forgalmat, meghatározhat egy állapot-mintavételt is. Az alapszintű Load Balancer IPv4-alapú mintavételt használ az IPv4-és IPv6-végpontok állapotának felmérésére a virtuális gépeken. A standard Load Balancer támogatja a explicit IPv6 Health-mintavételeket.

Hozzon létre egy terheléselosztó-szabályt az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Az alábbi példa létrehozza a *dsLBrule_v4* nevű terheléselosztó-szabályokat, és *dsLBrule_v6* és kiegyensúlyozza az *80* -as *TCP* -port forgalmát az IPv4-és IPv6-előtérbeli IP-konfigurációkhoz:

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Hozzon létre egy standard Load Balancert a [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Az alábbi példa egy *myInternalLoadBalancer* nevű nyilvános standard Load Balancer hoz létre az IPv4-és IPv6-ELŐTÉRBELI IP-konfigurációk, backend-készletek és terheléselosztási szabályok alapján, amelyeket az előző lépésekben hozott létre:

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Néhány virtuális gép üzembe helyezése és a Balancer tesztelése előtt létre kell hoznia a támogató hálózati erőforrásokat – rendelkezésre állási készletet, a hálózati biztonsági csoportot és a virtuális hálózati adaptereket. 

### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása
Az alkalmazás magas rendelkezésre állásának javítása érdekében helyezze a virtuális gépeket egy rendelkezésre állási csoportba.

Hozzon létre egy rendelkezésre állási készletet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). A következő példa egy *dsAVset* nevű rendelkezésre állási készletet hoz létre:

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a VNet bejövő és kimenő kommunikációját szabályozó szabályokhoz.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Hálózati biztonságicsoport-szabály létrehozása a 3389-es porhoz

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt, amely engedélyezi az RDP-kapcsolatokat a 3389-as porton a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Hálózati biztonságicsoport-szabály létrehozása a 80-as porhoz

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt, amely engedélyezi az internetkapcsolatot az 80-as porton a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>Hálózati adapterek létrehozása

Hozzon létre virtuális hálózati adaptereket a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Az alábbi példa két virtuális hálózati adaptert hoz létre IPv4-és IPv6-konfigurációval. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során).

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

Most már létrehozhatja a virtuális gépeket a [New-AzVM](/powershell/module/az.compute/new-azvm)használatával. A következő példa két virtuális gépet hoz létre, és a szükséges virtuális hálózati összetevőket, ha azok még nem léteznek.

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>IPv6-alapú kettős verem virtuális hálózatának megtekintése Azure Portal
Az IPv6 kettős verem virtuális hálózatát a következőképpen tekintheti meg Azure Portalban:
1. A portál keresési sávján adja meg a *dsVnet*.
2. Ha a **dsVnet** megjelenik a keresési eredmények között, válassza ki. Ez elindítja a *dsVnet* nevű kettős verem virtuális hálózat **Áttekintés** lapját. A kettős verem virtuális hálózata két hálózati adaptert jelenít meg, amelyek IPv4-és IPv6-konfigurációval rendelkeznek, amelyek a *dsSubnet* nevű kettős verem alhálózatában találhatók.

![IPv6 kettős verem Virtual Network standard belső Load Balancer](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> Az Azure-beli virtuális hálózat IPv6-értéke csak olvashatóként érhető el a Azure Portal ebben az előzetes kiadásban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás eltávolításához.

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehozta a standard Load Balancer egy kettős előtér-IP-konfigurációval (IPv4 és IPv6). Létrehozott két virtuális gépet is, amelyek a terheléselosztó háttér-készletéhez hozzáadott kettős IP-konfigurációval (IPV4 + IPv6) rendelkező hálózati adaptereket tartalmaznak. További információ az Azure-beli virtuális hálózatok IPv6-támogatásáról: [Mi az IPv6 for azure Virtual Network?](ipv6-overview.md)