---
title: Virtuális gép létrehozása általános rendszerképből
description: Hozzon létre egy virtuális gépet egy megosztott rendszerképben található általánosított rendszerkép használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: dbe1e31a9b56f2f60edbebd8286bf0bfdeaa327e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560964"
---
# <a name="create-a-vm-using-a-generalized-image"></a>Virtuális gép létrehozása általánosított rendszerkép használatával 

Hozzon létre egy virtuális gépet egy megosztott képtárban tárolt általánosított rendszerképből. Ha speciális rendszerkép használatával szeretne virtuális gépet létrehozni, tekintse meg [a virtuális gép létrehozása speciális rendszerképből](vm-specialized-image-version-powershell.md)című témakört.

Ha általánosított rendszerkép-verziót használ, létrehozhat egy vagy több új virtuális gépet. A [New-AzVM](/powershell/module/az.compute/new-azvm) parancsmag használata. 

Ebben a példában a rendszerkép-definíció AZONOSÍTÓját használjuk annak biztosítására, hogy az új virtuális gép a rendszerkép legújabb verzióját fogja használni. Egy adott verziót is használhat a rendszerkép-verziójának AZONOSÍTÓjának használatával `Set-AzVMSourceImage -Id` . Például a következő rendszerkép *-verzió használata* `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` : 

Vegye figyelembe, hogy egy adott rendszerkép-verzió használata azt jelenti, hogy az Automation sikertelen lehet, ha az adott rendszerkép verziója nem érhető el, mert törölték vagy eltávolították a régióból. Javasoljuk, hogy az új virtuális gép létrehozásához használja a rendszerkép-definíció AZONOSÍTÓját, kivéve, ha egy adott rendszerkép-verzióra van szükség.

A példákban szükség szerint cserélje le az erőforrásnevek nevét. 

## <a name="simplified-parameter-set"></a>Egyszerűsített paraméter-készlet

Az egyszerűsített paraméter használatával gyorsan létrehozhat egy virtuális gépet egy rendszerképből. Az egyszerűsített paraméterérték a virtuális gép nevét használja a szükséges erőforrások (például a vNet és a nyilvános IP-cím) automatikus létrehozásához. 

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name $vmName `
   -Image $imageDefinition.Id
   -Credential $cred
```



## <a name="full-parameter-set"></a>Teljes paraméter beállítva

Az adott erőforrásokkal rendelkező virtuális gépeket a teljes paraméter készlet használatával hozhatja létre.

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

# Network pieces
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
   -DestinationPortRange 3389 `
   -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name myNic `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageDefinition.Id to use the latest image version.
$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```


## <a name="next-steps"></a>Következő lépések
Az [Azure rendszerkép-szerkesztő (előzetes verzió)](./image-builder-overview.md) segítségével automatizálhatja a rendszerkép-verziók létrehozását, és [egy meglévő rendszerkép-verzióból](./windows/image-builder-gallery-update-image-version.md)is frissítheti és létrehozhatja az új rendszerkép verzióját. 

Sablonok használatával is létrehozhat megosztott képgyűjteményi erőforrásokat. Több Azure Gyorsindítás-sablon is elérhető: 

- [Shared Image Gallery létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerképverzió létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása rendszerképverzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

A megosztott képtárakkal kapcsolatos további információkért tekintse meg az [áttekintést](./shared-image-galleries.md). Ha problémákba ütközik, tekintse meg a [megosztott képtárak hibaelhárítása](troubleshooting-shared-images.md)című témakört.