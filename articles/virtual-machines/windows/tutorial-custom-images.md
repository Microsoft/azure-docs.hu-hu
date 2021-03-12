---
title: Oktatóanyag – egyéni virtuálisgép-rendszerképek létrehozása a Azure PowerShell
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure PowerShellt egy Azure megosztott rendszerkép-katalógusban tárolt egyéni Windowsos virtuálisgép-rendszerkép létrehozásához.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 145b7300272243099cfccad0c99d8f69e83e6762
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555822"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>Oktatóanyag: Windowsos virtuális gépek rendszerképeinek létrehozása a Azure PowerShell

A lemezképek a központi telepítések rendszerindítására és a több virtuális gép közötti konzisztencia biztosítására használhatók. Ebben az oktatóanyagban egy Azure-beli virtuális gép saját speciális rendszerképét hozza létre a PowerShell használatával, és egy megosztott képtárban tárolja. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Shared Image Gallery létrehozása
> * Rendszerkép-definíció létrehozása
> * Rendszerképverzió létrehozása
> * Virtuális gép létrehozása rendszerképből 
> * Képtár megosztása



## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépések részletesen ismertetik, hogyan készíthet egy meglévő virtuális gépet, és hogyan alakíthatja át újra felhasználható egyéni rendszerképeket, amelyeket új virtuális gépek létrehozásához használhat.

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre. Ha szükséges, a [PowerShell](quick-create-powershell.md) rövid útmutatójában létrehozhat egy virtuális gépet, amelyet ehhez az oktatóanyaghoz szeretne használni. Az oktatóanyagban végzett munka során cserélje le az erőforrás nevét, ahol szükséges.

## <a name="overview"></a>Áttekintés

A [megosztott képgyűjtemény](../shared-image-galleries.md) egyszerűbbé teszi a szervezeten belüli Egyéni rendszerképek megosztását. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. 

A megosztott képkatalógus lehetővé teszi az egyéni virtuálisgép-rendszerképek megosztását másokkal. Válassza ki a megosztani kívánt képeket, mely régiókat szeretné elérhetővé tenni a alkalmazásban, és hogy kivel szeretné megosztani azokat. 

A megosztott képkatalógus funkció több erőforrástípust tartalmaz:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="get-the-vm"></a>A virtuális gép beszerzése

A [Get-AzVM](/powershell/module/az.compute/get-azvm)használatával megtekintheti az erőforráscsoporthoz elérhető virtuális gépek listáját. Ha ismeri a virtuális gép nevét és az erőforráscsoportot, használhatja `Get-AzVM` újra a virtuálisgép-objektum beolvasásához és egy változóban való tárolásához. Ez a példa egy *sourceVM* nevű virtuális gépet kap a "myResourceGroup" erőforráscsoporthoz, és hozzárendeli azt a (z) *$sourceVM* változóhoz. 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal.

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myGalleryRG* nevű erőforráscsoportot a *EastUS* régióban:

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>Rendszerkép-gyűjtemény létrehozása 

A képgyűjtemény a képmegosztás engedélyezéséhez használt elsődleges erőforrás. A katalógus nevének megengedett karaktere nagybetűs vagy kisbetűk, számjegyek, pontok és időszakok. A gyűjtemény neve nem tartalmazhat kötőjeleket. A katalógus nevének egyedinek kell lennie az előfizetésen belül. 

Hozzon létre egy képtárat a [New-AzGallery](/powershell/module/az.compute/new-azgallery)használatával. A következő példában létrehozunk egy *MyGallery* nevű katalógust a *myGalleryRG* -erőforráscsoporthoz.

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása 

A rendszerkép-definíciók logikai csoportosítást hoznak létre a képekhez. Ezek az adatok a bennük létrehozott rendszerkép-verziókra vonatkozó információk kezelésére szolgálnak. A képdefiníciók nevei nagybetűket, kisbetűket, számokat, pontokat, kötőjeleket és pontokat tartalmazhatnak. További információ a képdefiníciók által megadható értékekről: [képdefiníciók](../shared-image-galleries.md#image-definitions).

Hozza létre a rendszerkép definícióját a [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion)használatával. Ebben a példában a katalógus képének neve *myGalleryImage* , és egy speciális képhez lett létrehozva. 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Rendszerképverzió létrehozása

Hozzon létre egy rendszerkép-verziót egy virtuális gépről a [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)használatával. 

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ebben a példában a rendszerkép verziója a *1.0.0* , és a rendszer az *USA keleti* és *déli középső* régiójában lévő adatközpontokra replikálja. A célcsoportok replikáláshoz való kiválasztásakor meg kell adnia a *forrás* régiót a replikálás célhelye.

A virtuális gépről származó rendszerkép-verzió létrehozásához használja a `$vm.Id.ToString()` következőt: `-Source` .

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVM.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

Eltarthat egy ideig, amíg a rendszer replikálja a rendszerképet az összes megcélzott régióba.


## <a name="create-a-vm"></a>Virtuális gép létrehozása 

Ha speciális rendszerképet használ, létrehozhat egy vagy több új virtuális gépet. A [New-AzVM](/powershell/module/az.compute/new-azvm) parancsmag használata. A rendszerkép használatához használja `Set-AzVMSourceImage` és állítsa be a `-Id` lemezkép-definíció azonosítóját (ebben az esetben a $GalleryImage. ID azonosítót), hogy mindig a legújabb lemezkép-verziót használja. 

Szükség szerint cserélje le az erőforrás-neveket ebben a példában. 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```


## <a name="share-the-gallery"></a>A katalógus megosztása

Javasoljuk, hogy a Képtár szintjén ossza meg a hozzáférést. Használjon e-mail-címet és a [Get-AzADUser](/powershell/module/az.resources/get-azaduser) parancsmagot a felhasználó objektumazonosító beszerzéséhez, majd a [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) használatával adja meg nekik a katalógushoz való hozzáférést. Cserélje le a példában szereplő e-mailt a alinne_montes@contoso.com saját adataira.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```
   
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmagot az erőforráscsoport és az összes kapcsolódó erőforrás eltávolításához:

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Az Azure a csomagoló, az [Azure VM rendszerkép-készítő](../image-builder-overview.md)szolgáltatásra épülő szolgáltatást is kínál. Egyszerűen írja le a testreszabásokat egy sablonban, és kezeli a képek létrehozását. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy speciális virtuálisgép-rendszerképet. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Shared Image Gallery létrehozása
> * Rendszerkép-definíció létrehozása
> * Rendszerképverzió létrehozása
> * Virtuális gép létrehozása rendszerképből 
> * Képtár megosztása

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre magasan elérhető virtuális gépeket.

> [!div class="nextstepaction"]
> [Magas rendelkezésre állású virtuális gépek létrehozása](tutorial-availability-sets.md)