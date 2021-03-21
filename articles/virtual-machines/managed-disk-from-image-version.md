---
title: Felügyelt lemez létrehozása lemezkép-verzióból
description: Felügyelt lemez létrehozása lemezkép-verzióból egy megosztott rendszerkép-gyűjteményben.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 359816bbf3fb93a4ec2bfa3092adbb2236ae2f07
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562554"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Felügyelt lemez létrehozása lemezkép-verzióból

Ha szükséges, exportálhatja az operációs rendszert vagy egy adatlemezt egy lemezkép-verzióról felügyelt lemezként egy megosztott képtárban tárolt lemezkép-verzióból.


## <a name="cli"></a>parancssori felület

Egy katalógusban lévő rendszerkép-verziók listázása az [az SIG rendszerkép-Version List](/cli/azure/sig/image-version#az_sig_image_version_list)paranccsal. Ebben a példában az összes olyan rendszerkép-verziót keresjük, amelyek a *myImageDefinition* rendszerkép-definíció részét képezik a *MyGallery* -rendszerkép galériájában.

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Állítsa a `source` változót a lemezkép verziójának azonosítójára, majd az [az Disk Create](/cli/azure/disk#az_disk_create) paranccsal hozza létre a felügyelt lemezt. 

Ebben a példában a lemezkép operációsrendszer-lemezét exportáljuk egy *myManagedOSDisk* nevű felügyelt lemez létrehozásához a *EastUS* régióban egy *myResourceGroup* nevű erőforráscsoport alatt. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



Ha adatlemezt szeretne exportálni a rendszerkép verziójából, a Hozzáadás elemnél `--gallery-image-reference-lun` adja meg az exportálandó adatlemez LUN-helyét. 

Ebben a példában a lemezkép verziójának 0. logikai egységében található adatlemezt exportáljuk egy *myManagedDataDisk* nevű felügyelt lemez létrehozásához a *EastUS* régióban egy *myResourceGroup* nevű erőforráscsoport esetében. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

Katalógusban szereplő rendszerképek listázása a [Get-AzResource](/powershell/module/az.resources/get-azresource)használatával. 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

Ha már rendelkezik az összes szükséges információval, a [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) használatával lekérheti a használni kívánt forrás-rendszerkép verzióját, és hozzárendelheti azt egy változóhoz. Ebben a példában bemutatjuk a `1.0.0` definíció rendszerképének verzióját a forrás-katalógusban az `myImageDefinition` `myGallery` `myResourceGroup` erőforráscsoporthoz.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Miután beállította a `source` változót a lemezkép verziójának azonosítójára, a [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) használatával hozzon létre egy lemez-konfigurációt, és hozzon létre egy [új AzDisk](/powershell/module/az.compute/new-azdisk) a lemez létrehozásához. 

Ebben a példában a lemezkép operációsrendszer-lemezét exportáljuk egy *myManagedOSDisk* nevű felügyelt lemez létrehozásához a *EastUS* régióban egy *myResourceGroup* nevű erőforráscsoport alatt. 

Hozzon létre egy lemez-konfigurációt.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

Hozza létre a lemezt.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

Ha adatlemezt szeretne exportálni a lemezkép verziójában, adjon hozzá egy LUN-azonosítót a lemez konfigurációjához, és adja meg az exportálandó adatlemez logikai egységének helyét. 

Ebben a példában a lemezkép verziójának 0. logikai egységében található adatlemezt exportáljuk egy *myManagedDataDisk* nevű felügyelt lemez létrehozásához a *EastUS* régióban egy *myResourceGroup* nevű erőforráscsoport esetében. 

Hozzon létre egy lemez-konfigurációt.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

Hozza létre a lemezt.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>Következő lépések

A felügyelt lemezekről az [Azure CLI](image-version-managed-image-cli.md) vagy a [PowerShell](image-version-managed-image-powershell.md)használatával is létrehozhat lemezkép-verziót.


