---
title: Rendszerkép másolása egy másik gyűjteményből a PowerShell használatával
description: Rendszerkép másolása egy másik gyűjteményből Azure PowerShell használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: d9bbe40e35bdad6fac5c5ccb0b15b909e77b938c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564016"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>Rendszerkép másolása egy másik gyűjteményből a PowerShell használatával

Ha a szervezet több gyűjteményt is tartalmaz, létrehozhat képeket a más gyűjteményekben tárolt képekből. Előfordulhat például, hogy egy fejlesztési és tesztelési galériával új képeket szeretne létrehozni és tesztelni. Ha készen állnak az éles környezetben való használatra, az alábbi példán keresztül másolhatja őket egy éles katalógusba. Az [Azure CLI](image-version-another-gallery-cli.md)használatával is létrehozhat egy képet egy másik katalógusban található rendszerképből.


## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához rendelkeznie kell egy meglévő forrás-és képdefinícióval, valamint a rendszerkép verziószámával. Emellett a célhelyen is szerepelnie kell. 

A forrás-rendszerkép verzióját replikálni kell arra a régióra, ahol a célhely található. 

A rendszer létrehoz egy új rendszerkép-definíciót és egy rendszerkép-verziót a célhelyen.


A cikkben végzett munka során szükség esetén cserélje le az erőforrások nevét.


## <a name="get-the-source-image"></a>A forrás rendszerképének beolvasása 

Információra van szüksége a forrás rendszerkép-definíciójában, hogy másolatot készítsen belőle a katalógusból.

A [Get-AzResource](/powershell/module/az.resources/get-azresource) parancsmaggal megjelenítheti a meglévő galériák, képdefiníciók és képverziók adatait.

Az eredmények formátuma `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

Ha már rendelkezik az összes szükséges információval, a [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion)használatával lekérheti a forrás rendszerkép VERZIÓjának azonosítóját. Ebben a példában bemutatjuk a `1.0.0` definíció rendszerképének verzióját a forrás-katalógusban az `myImageDefinition` `myGallery` `myResourceGroup` erőforráscsoporthoz.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>A rendszerkép definíciójának létrehozása 

Létre kell hoznia egy új rendszerkép-definíciót, amely megfelel a forrás képdefiníciójának. A [Get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition)használatával megtekintheti a rendszerkép definíciójának újbóli létrehozásához szükséges összes információt.

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


A kimenet a következőhöz hasonlóan fog kinézni:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Hozzon létre egy új rendszerkép-definíciót a célhelyen a [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) parancsmag és a fenti kimenetből származó információk használatával.


Ebben a példában a képdefiníció neve *myDestinationImgDef* a *myDestinationGallery* nevű galériában.


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1 `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

Hozzon létre egy rendszerkép [-verziót a New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)használatával. Meg kell adnia a forrás rendszerkép AZONOSÍTÓját a `--managed-image` (z) paraméterben a rendszerkép verziójának a célhelyen való létrehozásához. 

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ebben a példában a *myDestinationGallery* neve a *myDestinationRG* erőforráscsoport, az *USA nyugati* régiója. A rendszerkép verziója a *1.0.0* , és 1 replikát fogunk létrehozni az *USA déli középső* régiójában és 2 replikát az *USA nyugati* régiójában. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -Source $sourceImgVer.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -asJob 
```

Eltarthat egy ideig, amíg replikálja a rendszerképet az összes megcélzott régióba, így létrehoztunk egy feladatot, hogy nyomon kövessük a folyamat előrehaladását. A feladatok előrehaladásának megtekintéséhez írja be a következőt: `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült és replikálva lett ahhoz, hogy ugyanazt a felügyelt képet használhassa egy másik rendszerkép-verzió létrehozásához.
>
> A rendszerképet a prémium tárolóban is tárolhatja egy hozzáadási `-StorageAccountType Premium_LRS` vagy a [zóna redundáns tárterületével](../storage/common/storage-redundancy.md) , `-StorageAccountType Standard_ZRS` Ha létrehozza a rendszerkép verzióját.
>


## <a name="next-steps"></a>Következő lépések

Hozzon létre egy virtuális gépet [általánosított](vm-generalized-image-version-powershell.md) vagy [speciális](vm-specialized-image-version-powershell.md) rendszerkép-verzióból.

Az [Azure rendszerkép-szerkesztő (előzetes verzió)](./image-builder-overview.md) segítségével automatizálhatja a rendszerkép-verziók létrehozását, és [egy meglévő rendszerkép-verzióból](./linux/image-builder-gallery-update-image-version.md)is frissítheti és létrehozhatja az új rendszerkép verzióját. 

A vásárlási tervre vonatkozó információk megadásával kapcsolatos információkért lásd: [Azure Marketplace vásárlási terv információinak megadása képek létrehozásakor](marketplace-images.md).