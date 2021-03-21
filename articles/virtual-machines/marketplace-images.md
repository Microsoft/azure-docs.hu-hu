---
title: Piactér-vásárlási terv adatainak meghatározása Azure PowerShell használatával
description: Megtudhatja, hogyan adhatja meg az Azure Marketplace vásárlási terv részleteit a lemezképek megosztott képtárban való létrehozásakor.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 9df1f6b02e6572c8f2153016c0142912e24fcfe8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562537"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>Adja meg az Azure Marketplace vásárlási terv információit képek létrehozásakor

Ha megosztott katalógusban hoz létre egy rendszerképet, az eredetileg egy Azure Marketplace-rendszerképből létrehozott forrás használatával lehet, hogy nyomon kell követnie a vásárlási tervre vonatkozó információkat. Ez a cikk bemutatja, hogyan keresheti meg a virtuális gép vásárlási terveit, majd hogyan használhatja ezeket az információkat a rendszerkép-definíciók létrehozásakor. A rendszerkép-definícióban található információk használatával egyszerűbbé válik a vásárlási terv információinak beszerzése, amikor virtuális gépet hoz létre egy rendszerképhez.

A Piactéri rendszerképek keresésével és használatával kapcsolatos további információkért lásd: [Azure Marketplace-rendszerképek keresése és használata](./windows/cli-ps-findimage.md).


## <a name="get-the-source-vm-information"></a>A forrásként szolgáló virtuális gép adatainak beolvasása
Ha még mindig rendelkezik az eredeti virtuális géppel, a Get-AzVM használatával lekérheti a terv nevét, a közzétevőt és a termék információit. Ez a példa egy *myVM* nevű virtuális gépet olvas be a *myResourceGroup* -erőforráscsoporthoz, majd megjeleníti a virtuális gép vásárlási tervének információit.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

## <a name="create-the-image-definition"></a>A rendszerkép definíciójának létrehozása

Szerezze be a rendszerkép tárolására használni kívánt képtárat. Először is listázhatja az összes gyűjteményt.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Ezután hozzon létre változókat a használni kívánt gyűjteményhez. Ebben a példában egy MyGallery nevű változót hozunk létre `$gallery` a  *myGalleryRG* -erőforráscsoporthoz.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

Hozza létre a rendszerkép definícióját a  `-PurchasePlanPublisher` ,, `-PurchasePlanProduct` és `-PurchasePlanName` paraméterek használatával.

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

Ezután hozza létre a rendszerkép verzióját a [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)használatával. Létrehozhat egy rendszerkép-verziót egy [virtuális](image-version-vm-powershell.md#create-an-image-version)gépről, egy [felügyelt rendszerképből](image-version-managed-image-powershell.md#create-an-image-version), egy [VHD\snapshot](image-version-snapshot-powershell.md#create-an-image-version)vagy [egy másik rendszerkép-verzióból](image-version-another-gallery-powershell.md#create-the-image-version). 


## <a name="create-the-vm"></a>A virtuális gép létrehozása

Ha a rendszerképből hoz létre virtuális gépet, a rendszerkép definíciójában található információk segítségével továbbíthatja a közzétevő adatait a [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan)használatával.


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>Következő lépések

A Piactéri rendszerképek keresésével és használatával kapcsolatos további információkért lásd: [Azure Marketplace-rendszerképek keresése és használata](./windows/cli-ps-findimage.md).
