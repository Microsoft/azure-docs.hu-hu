---
title: PowerShell – rendszerkép létrehozása pillanatképből vagy felügyelt lemezről megosztott képtárban
description: Megtudhatja, hogyan hozhat létre lemezképet egy pillanatképből vagy egy felügyelt lemezből egy megosztott rendszerkép-katalógusban a PowerShell használatával.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 70ef194ab7f7403f31ad431eba25db5f71dfb580
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556903"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>Rendszerkép létrehozása felügyelt lemezről vagy pillanatképből egy megosztott rendszerkép-katalógusban a PowerShell használatával

Ha van meglévő pillanatképe vagy felügyelt lemeze, amelyet át szeretne telepíteni egy megosztott képkatalógusba, akkor közvetlenül a felügyelt lemezről vagy pillanatképből hozhat létre egy megosztott rendszerkép-katalógust. Az új rendszerkép tesztelése után törölheti a forrás felügyelt lemezt vagy pillanatképet. Az [Azure CLI](image-version-snapshot-cli.md)használatával is létrehozhat egy rendszerképet egy felügyelt lemezről vagy pillanatképből egy megosztott Képtárban.

A képkatalógusban található lemezképek két összetevővel rendelkeznek, amelyeket a következő példában hozunk létre:
- A **rendszerkép definíciója** információt nyújt a rendszerképekről és az azok használatára vonatkozó követelményekről. Ez magában foglalja azt is, hogy a rendszerkép Windows vagy Linux, specializált vagy általánosított, kibocsátási megjegyzések, valamint minimális és maximális memória-követelmény. Ez egy adott típusú rendszerkép definíciója. 
- A **rendszerkép verziója** a virtuális gépek megosztott képtárat használó létrehozásához használatos. A környezethez szükség lehet a rendszerkép több verziójára. A virtuális gép létrehozásakor a rendszer a rendszerkép verzióját használja új lemezek létrehozásához a virtuális géphez. A rendszerkép verziója többször is használható.


## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez pillanatkép vagy felügyelt lemez szükséges. 

Ha adatlemezt szeretne megadni, az adatlemez mérete nem lehet nagyobb 1 TB-nál.

A cikkben végzett munka során szükség esetén cserélje le az erőforrások nevét.


## <a name="get-the-snapshot-or-managed-disk"></a>A pillanatkép vagy a felügyelt lemez beolvasása

A [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot)használatával megtekintheti az erőforráscsoporthoz elérhető Pillanatképek listáját. 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

Ha ismeri a pillanatkép nevét és a benne található erőforráscsoportot, akkor `Get-AzSnapshot` ismét használhatja a pillanatkép-objektum beolvasását és egy változóban történő tárolását. Ebben a példában egy *mySnapshot* nevű pillanatképet kap a "myResourceGroup" erőforráscsoporthoz, és hozzárendeli azt a *$Source* változóhoz. 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

A pillanatképek helyett felügyelt lemezt is használhat. Felügyelt lemez beszerzéséhez használja a [Get-AzDisk](/powershell/module/az.compute/get-azdisk). 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

Ezután szerezze be a felügyelt lemezt, és rendelje hozzá a `$source` változóhoz.

```azurepowershell-interactive
$source = Get-AzDisk `
   -Name myDisk
   -ResourceGroupName myResourceGroup
```

Ugyanazokat a parancsmagokat használhatja a rendszerképbe felvenni kívánt adatlemezek lekéréséhez. Rendelje hozzá a változóhoz, majd ezeket a változókat később, amikor létrehozza a rendszerkép verzióját.


## <a name="get-the-gallery"></a>A katalógus beszerzése

Az összes gyűjteményt és képdefiníciót név szerint listázhatja. Az eredmények formátuma `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Ha megtalálta a megfelelő katalógust, hozzon létre egy változót, amelyet később szeretne használni. Ez a példa a *MyGallery* nevű katalógust olvassa be a *myResourceGroup* erőforráscsoporthoz.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása 

A rendszerkép-definíciók logikai csoportosítást hoznak létre a képekhez. A rendszer a rendszerképpel kapcsolatos információk kezelésére szolgál. A képdefiníciók nevei nagybetűket, kisbetűket, számokat, pontokat, kötőjeleket és pontokat tartalmazhatnak. 

A rendszerkép meghatározásakor győződjön meg arról, hogy a megfelelő információval rendelkezik. Ebben a példában feltételezzük, hogy a pillanatkép vagy a felügyelt lemez egy használatban lévő virtuális gépről származik, és nem lett általánosítva. Ha a felügyelt lemez vagy pillanatkép általánosított operációs rendszerből készült (a Sysprep futtatása után Windows vagy [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` vagy `-deprovision+user` Linux rendszeren), akkor módosítsa a alkalmazást `-OsState` `generalized` . 

További információ a képdefiníciók által megadható értékekről: [képdefiníciók](./shared-image-galleries.md#image-definitions).

Hozza létre a rendszerkép definícióját a [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion)használatával. Ebben a példában a képdefiníció neve *myImageDefinition*, és egy speciális Windows operációs rendszerhez készült. A rendszerképek definíciójának létrehozásához használja a következőt: `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

### <a name="purchase-plan-information"></a>Vásárlási terv információi

Bizonyos esetekben a vásárlási terv adatait kell átadnia, amikor virtuális gépet hoz létre egy Azure Marketplace-rendszerképen alapuló rendszerképből. Ezekben az esetekben azt javasoljuk, hogy a vásárlási terv adatait a rendszerkép definíciójában adja meg. Ebben az esetben a [lemezképek létrehozásakor az Azure Marketplace vásárlási terv információinak beszerzése](marketplace-images.md)című témakörben talál információt.


## <a name="create-an-image-version"></a>Rendszerképverzió létrehozása

Hozzon létre egy rendszerkép-verziót a pillanatképből a [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)használatával. 

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ha azt szeretné, hogy a lemezkép tartalmazzon egy adatlemezt, az operációsrendszer-lemez mellett adja hozzá a `-DataDiskImage` paramétert, és állítsa be az adatlemez-pillanatkép vagy a felügyelt lemez azonosítójának megfelelően.

Ebben a példában a rendszerkép verziója a *1.0.0* , és a rendszer replikálja az *USA nyugati középső* régiójában és az *USA déli középső* régiójában. A célcsoportok replikáláshoz való kiválasztásakor ne feledje, hogy a *forrás* régiót is meg kell adnia a replikálás céljának.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

Eltarthat egy ideig, amíg replikálja a rendszerképet az összes megcélzott régióba, így létrehoztunk egy feladatot, hogy nyomon kövessük a folyamat előrehaladását. A feladatok előrehaladásának megtekintéséhez írja be a következőt: `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült a létrehozás és a replikálás során, mielőtt ugyanezt a pillanatképet használhatja egy másik rendszerkép-verzió létrehozására. 
>
> A rendszerkép verzióját a [zóna redundáns tárolójában](../storage/common/storage-redundancy.md) is tárolhatja `-StorageAccountType Standard_ZRS` a rendszerkép verziójának létrehozásakor.
>

## <a name="delete-the-source"></a>A forrás törlése

Miután meggyőződött arról, hogy az új rendszerkép verziója megfelelően működik, törölheti a rendszerkép forrását a [Remove-AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) vagy a [Remove-AzDisk](/powershell/module/az.compute/remove-azdisk)használatával.


## <a name="next-steps"></a>Következő lépések

Miután meggyőződött arról, hogy a replikáció elkészült, létrehozhat egy virtuális gépet a [speciális rendszerképből](vm-specialized-image-version-powershell.md).

A vásárlási tervre vonatkozó információk megadásával kapcsolatos információkért lásd: [Azure Marketplace vásárlási terv információinak megadása képek létrehozásakor](marketplace-images.md).