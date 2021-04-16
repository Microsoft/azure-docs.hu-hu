---
title: Rendszerkép másolása másik katalógusból a PowerShell használatával
description: Kép másolása másik katalógusból a Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 35346836767bc1da8c498e23fd3b42afe7a9c350
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531192"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>Rendszerkép másolása másik katalógusból a PowerShell használatával

Ha a szervezetben több katalógus is található, más katalógusban tárolt képekből is létrehozhat képeket. Tegyük fel például, hogy fejlesztési és tesztelési katalógusa van az új lemezképek létrehozásához és teszteléséhez. Ha készen állnak az éles környezetben való használatra, az alábbi példával átmásolhatja őket egy éles katalógusba. Lemezképet egy másik katalógusban található rendszerképből is létrehozhat az [Azure CLI használatával.](image-version-another-gallery-cli.md)


## <a name="before-you-begin"></a>Előkészületek

A cikk befejezéséhez egy meglévő forráskatatárra, rendszerkép-definícióra és rendszerképverzióra van lehetőség. Emellett egy célgalériának is kell lennie. 

A forrás rendszerkép verzióját replikálni kell arra a régióra, ahol a célkatatár található. 

A célkatatárban új rendszerkép-definíciót és rendszerképverziót hozunk létre.


A cikk munka közben szükség esetén cserélje le az erőforrásneveket.


## <a name="get-the-source-image"></a>A forrás rendszerképének lekérte 

A forrásként használt rendszerkép definíciójának információira lesz szüksége, hogy másolatot hoz létre róla a célkatatárban.

A [Get-AzResource](/powershell/module/az.resources/get-azresource) parancsmaggal listába sorolja a meglévő katalógusokkal, rendszerképdefiníciókkal és rendszerképverziókkal kapcsolatos információkat.

Az eredmények formátuma `gallery\image definition\image version` : .

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

Ha minden szükséges információ megvan, a [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion)használatával le tudja szerezni a forrásként használt rendszerképverzió azonosítóját. Ebben a példában a definíció rendszerképverzióját kérjük le a `1.0.0` `myImageDefinition` `myGallery` forráskatatárban, az `myResourceGroup` erőforráscsoportban.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>A rendszerkép-definíció létrehozása 

Létre kell hoznia egy új képdefiníciót, amely megfelel a forrás képdefiníciójának. A [get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition)használatával minden információt láthat, amely a rendszerkép-definíció újbóli létrehozása érdekében szükséges.

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

Hozzon létre egy új rendszerkép-definíciót a célkatatárban a [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) parancsmaggal és a fenti kimenetből származó információkkal.


Ebben a példában a képdefiníció neve *myDestinationImgDef* a *myDestinationGallery nevű katalógusban.*


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

Rendszerképverzió létrehozása a [New-AzGalleryImageVersion használatával.](/powershell/module/az.compute/new-azgalleryimageversion) A rendszerkép verziójának a célkatatárban való létrehozásához meg kell adni a forrásként megadott rendszerkép azonosítóját a `-Source` paraméterben. 

A képverzió megengedett karakterei a számok és a időszakok. A számoknak egy 32 bites egész szám tartományán belül kell lennie. Formátum: *Főverzió.* *MinorVersion (Alverzió).* *Javítás.*

Ebben a példában a célgyűjtemény neve *myDestinationGallery*, a *myDestinationRG* erőforráscsoportban, az *USA nyugati régiója helyen.* A rendszerkép verziója *1.0.0,* és 1 replikát  fogunk létrehozni az USA déli középső régiójában, és 2 replikát az USA nyugati *régiójában.* 


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

A rendszerkép replikálása az összes célrégióba kis ideig is tart, ezért létrehoztunk egy feladatot, hogy nyomon követjük a folyamat előrehaladását. A feladat előrehaladásának a következőt kell begépelnie: `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép teljes létrehozása és replikálása befejeződik, mielőtt ugyanezt a felügyelt rendszerképet egy másik rendszerképverzió létrehozására használhatja.
>
> A rendszerképet a rendszerképverzió létrehozásakor hozzáadott hozzáadásával vagy zónaredundáns tárolással is tárolhatja az előzetes `-StorageAccountType Premium_LRS` [](../storage/common/storage-redundancy.md) `-StorageAccountType Standard_ZRS` tárolóban.
>


## <a name="next-steps"></a>Következő lépések

Virtuális gép létrehozása [általános vagy](vm-generalized-image-version-powershell.md) specializált rendszerképverzióból. [](vm-specialized-image-version-powershell.md)

[Az Azure Image Builder (előzetes verzió)](./image-builder-overview.md) segíthet automatizálni a rendszerképverzió létrehozását, sőt akár egy meglévő rendszerképverzióból is frissíthet és létrehozhat egy új [rendszerképverziót.](./linux/image-builder-gallery-update-image-version.md) 

További információ a vásárlási terv információinak megszolgáltatásról: A vásárlási terv Azure Marketplace meg [a rendszerképek létrehozásakor.](marketplace-images.md)
