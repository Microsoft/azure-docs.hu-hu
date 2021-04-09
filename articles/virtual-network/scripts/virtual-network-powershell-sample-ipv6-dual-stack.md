---
title: Azure PowerShell parancsfájl-minta – IPv6-végpontok konfigurálása
titlesuffix: Azure Virtual Network
description: Konfigurálja az IPv6-végpontokat a virtuális hálózatban egy Azure PowerShell parancsfájllal, és keresse meg a PowerShell-minta segítségét szolgáló, a parancsra vonatkozó dokumentációra mutató hivatkozásokat.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b4d7861c7b4042f9143c7497ee62ded6963b42e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550776"
---
# <a name="configure-ipv6-endpoints-in-virtual-network-with-azure-powershell-script-sample"></a>IPv6-végpontok konfigurálása virtuális hálózatban Azure PowerShell parancsfájl-mintával

Ez a cikk bemutatja, hogyan helyezhet üzembe egy kettős verem (IPv4 + IPv6) alkalmazást az Azure-ban, amely egy Dual stack-alhálózattal rendelkező kettős veremű virtuális hálózatot, egy kettős (IPv4 + IPv6-alapú) előtér-konfigurációkat, a kettős IP-konfigurációval rendelkező virtuális gépeket, a kettős hálózati biztonsági csoport szabályait és a kettős nyilvános IP-címeket tartalmazza.

A szkriptet az Azure [Cloud Shellben](https://shell.azure.com/powershell) vagy egy helyi PowerShell-telepítésből futtathatja. Ha helyileg használja a PowerShellt, a parancsfájlhoz az Azure az PowerShell-modul 1.0.0 vagy újabb verziója szükséges. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek
A Dual stack-alkalmazás Azure-ban való üzembe helyezése előtt az alábbi Azure PowerShell használatával csak egyszer kell konfigurálnia az előfizetést:

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

## <a name="sample-script"></a>Példaszkript



```azurepowershell
# Dual-Stack VNET with 2 VMs.ps1
#   Deploys Dual-stack (IPv4+IPv6) Azure virtual network with 2 VMs and Basic Load Balancer with IPv4 and IPv6 public IPs

# Create resource group to contain the deployment
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"

# Create the public IPs needed for the deployment
  $PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6

  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
    $RdpPublicIP_2 = New-AzPublicIpAddress `
    -Name "RdpPublicIP_2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -AllocationMethod Dynamic `
    -IpAddressVersion IPv4


# Create Basic Load Balancer
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
  
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "dsLbBackEndPool_v4"
$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "dsLbBackEndPool_v6"

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
  
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

# Create availability set
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
  
# Create network security group and rules
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

$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2

#Create virtual network and subnet
# Create dual stack subnet config
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","fd00:db8:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","fd00:db8:deca::/48"  `
  -Subnet $subnet
  
  #Create network interfaces (NICs)
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
    
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 



# Create virtual machines
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs"

$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2016-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2


#End Of Script

```
## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás:

```powershell
Remove-AzResourceGroup -Name <resourcegroupname> -Force
```
## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép, egy rendelkezésre állási csoport, egy terheléselosztó és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt hoz létre. Ez a konfiguráció a virtuális hálózat létrehozására szolgál. |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)  | Létrehoz egy nyilvános IP-címet egy statikus IP-címmel és egy hozzárendelt DNS-névvel. |
| [Új – AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)  | Azure-terheléselosztót hoz létre. |
| [Új – AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Terheléselosztói mintavételt hoz létre. A terheléselosztói mintavétel a terheléselosztó-készlet egyes virtuális gépeinek figyelésére használható. Ha valamelyik virtuális gép elérhetetlenné válik, a terheléselosztó nem irányít rá forgalmat. |
| [Új – AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Terheléselosztó-szabályt hoz létre. Ebben a példában egy, a 80-es portra vonatkozó szabály jön létre. A hálózati terheléselosztóra érkező HTTP-forgalom a terheléselosztó csoporthoz tartozó egyik virtuális gép 80-as portjára lesz irányítva. |
| [Új – AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Létrehoz egy hálózati biztonsági csoportot (NSG), amely biztonsági határként szolgál az internet és a virtuális gép között. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Létrehoz egy NSG-szabályt a befelé irányuló forgalom engedélyezésére. Ebben a példában a 22-es portot nyitjuk meg az SSH-forgalom számára. |
| [Új – AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Létrehoz egy virtuális hálózati kártyát, és csatlakoztatja a virtuális hálózathoz, az alhálózathoz és az NSG-hez. |
| [Új – AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Létrehoz egy rendelkezésre állási csoportot. A rendelkezésre állási csoportok biztosítják az alkalmazások üzemidőét azáltal, hogy a virtuális gépeket a fizikai erőforrások között terjesztik, így ha hiba történik, a teljes készletet nem érinti a rendszer. |
| [Új – AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Egy virtuálisgép-konfigurációt hoz létre. Ebben a konfigurációban olyan információk szerepelnek, mint a virtuális gép neve, az operációs rendszer és a rendszergazdai hitelesítő adatok. A rendszer a virtuális gépek létrehozása során használja ezt a konfigurációt. |
| [New-AzVM](/powershell/module/az.compute/new-azvm)  | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, a virtuális hálózathoz, az alhálózathoz és az NSG-hez. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/).

További hálózatkezelési PowerShell-példaszkripteket az [Azure-hálózatkezelés áttekintő dokumentációjában](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) találhat.