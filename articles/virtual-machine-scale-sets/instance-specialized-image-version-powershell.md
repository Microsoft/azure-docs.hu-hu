---
title: Méretezési csoport létrehozása speciális rendszerképből
description: Méretezési csoport létrehozása egy megosztott képtárban található speciális rendszerkép használatával.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: b47498127996698d53946eb2aaf54368132ae7f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98877985"
---
# <a name="create-a-scale-set-from-a-specialized-image-using-powershell"></a>Méretezési csoport létrehozása speciális rendszerképből a PowerShell használatával 

Hozzon létre egy virtuális gépet egy megosztott rendszerkép- [gyűjteményben](../virtual-machines/shared-image-galleries.md) tárolt speciális rendszerkép-verzióból Azure PowerShell használatával. Ha általánosított rendszerkép-verzió használatával szeretne létrehozni egy méretezési készletet, tekintse [meg a méretezési csoport példányainak általánosított rendszerkép-verzióból történő létrehozását](instance-generalized-image-version-powershell.md)ismertető témakört.

Ha a katalógusban egy speciális rendszerkép található, létrehozhat egy virtuálisgép-méretezési készletet a [New-AzVmss](/powershell/module/az.compute/new-azvmss) parancsmag használatával. 

Ebben a példában a rendszerkép-definíció AZONOSÍTÓját használjuk annak biztosítására, hogy az új virtuális gép a rendszerkép legújabb verzióját fogja használni. Egy adott verziót is használhat a rendszerkép-verziójának AZONOSÍTÓjának használatával `-ImageReferenceId` . Például a következő rendszerkép *-verzió használata* `-ImageReferenceId "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` : 

Vegye figyelembe, hogy egy adott rendszerkép-verzió használata azt jelenti, hogy az Automation sikertelen lehet, ha az adott rendszerkép verziója nem érhető el, mert törölték vagy eltávolították a régióból. Javasoljuk, hogy az új virtuális gép létrehozásához használja a rendszerkép-definíció AZONOSÍTÓját, kivéve, ha egy adott rendszerkép-verzióra van szükség.

Az alábbi példák létrehoznak egy *myScaleSet* nevű méretezési csoportot a *MyVMSSRG* erőforráscsoporthoz a *SouthCentralUS* helyen. A méretezési csoport a *myImageDefinition* rendszerképből lesz létrehozva a *myGalleryRG* erőforráscsoport *MyGallery* -rendszerkép galériájában. Ha a rendszer kéri, állítsa be a saját rendszergazdai hitelesítő adatait a méretezési csoport virtuálisgép-példányaihoz.



```azurepowershell-interactive
# Get the image definition

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myGalleryRG `
   -Name myImageDefinition
   
# Define variables for the scale set
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $imageDefinition.Id

# Complete the configuration
 
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.

## <a name="next-steps"></a>Következő lépések
Az [Azure rendszerkép-szerkesztő (előzetes verzió)](../virtual-machines/image-builder-overview.md) segítségével automatizálhatja a rendszerkép-verziók létrehozását, és [egy meglévő rendszerkép-verzióból](../virtual-machines/linux/image-builder-gallery-update-image-version.md)is frissítheti és létrehozhatja az új rendszerkép verzióját.  

Sablonok használatával is létrehozhat megosztott képgyűjteményi erőforrásokat. Több Azure Gyorsindítás-sablon is elérhető: 

- [Shared Image Gallery létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerképverzió létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

A megosztott képtárakkal kapcsolatos további információkért tekintse meg az [áttekintést](../virtual-machines/shared-image-galleries.md). Ha problémákba ütközik, tekintse meg a [megosztott képtárak hibaelhárítása](../virtual-machines/troubleshooting-shared-images.md)című témakört.